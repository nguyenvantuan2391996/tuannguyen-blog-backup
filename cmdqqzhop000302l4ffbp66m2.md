---
title: "Circuit Breaker Pattern – How to Protect Your System from Cascading Failures"
seoTitle: "Circuit Breaker Pattern – How to Protect Your System"
seoDescription: "The Circuit Breaker Pattern is a design pattern used to manage faults in distributed systems by preventing repeated failed requests to downstream services."
datePublished: Thu Jul 31 2025 02:00:26 GMT+0000 (Coordinated Universal Time)
cuid: cmdqqzhop000302l4ffbp66m2
slug: circuit-breaker-pattern-how-to-protect-your-system-from-cascading-failures
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1753885983430/a1398cf1-ee5a-4131-9849-311a70f79608.jpeg
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1753886899710/f603b75b-7a8d-46c5-a573-60ffec30c634.jpeg
tags: design-patterns, golang, circuit-breaker

---

<center><iframe width="560" height="315" src="https://www.youtube.com/embed/FyX5A_mduyw?si=jffGBf9MpOAGfU-v" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe></center>

A Rainy Weekend in Hanoi, 2025...

Just recording a bit of emotion to send through time.

Raindrops take me back to somewhere far away… Perhaps there’s nothing better than rain to touch our emotions. Every time it rains, I don’t know how you feel, but personally, it reminds me of this song. Each situation, each time, or each place triggers different yet familiar memories: the raindrops from that first meeting with someone I had quietly admired for years, the sudden downpour on a day I skipped school, the drizzly days that made it hard to concentrate on studying, or simply sitting under a roof watching the rain drip slowly.

Everyone feels something different from the rain or this song — it could be an image, a mood, a memory, or even a sadness or joy. I must admit, whenever I hear this melody, I find myself in memories of the rain.

# I. The Problem
My team is currently developing and operating a facial recognition system for Vinpearl, Melia, Marriott hotels, Vinwonders amusement parks, Safari, and other places in the Vinpearl ecosystem. The core function of the system is: when a guest checks in or buys an activity ticket, their face is registered in the hotel system. Then, to access gates and turnstiles at resorts or parks, they simply scan their face and walk in.

Because guests can access multiple parks after hotel check-in, the system needs to call the hotel’s API at each gate to verify access rights. Recently, our team has observed the issue that if a hotel’s network or server goes down, our system calling their API will receive a 500 error or timeout (currently set at 30s). This leads to long waits for guests, crowd buildup, and eventually, system congestion 😩😩😩

Every time this happens, the operations team manually removes the faulty hotel from the access-check list so our system can continue working with the other hotels. Once that hotel's system recovers, we manually re-add it. This manual intervention is time-consuming and reactive, not proactive — even though we have alert services (backend alerts via Teams, monitoring with Grafana, etc.), it's still difficult (and I don’t operate the system directly, so I’m not fully aware 😅).

# II. Requirements & Solutions
From this issue, the client and product owner requested that the system must automatically stop calling a hotel once it's detected as down, and then automatically resume calls once it's recovered. The handling must be proactive and automatic, with alerts sent via chat (like Teams), rather than waiting for customers to complain before acting.

So, the team sat down to discuss possible solutions. One operations teammate, someone who’s passionate and very responsible about the product, contributed several ideas:

- Create an API to add/remove faulty hotels from the access check list so incidents can be handled programmatically.
- Add a button to the CMS system to allow non-technical staff to remove hotels via UI.
- Create a scheduled health-check job that periodically checks the server. If an issue is found, call the API to remove the hotel; if the server recovers, call the API to add it back.

As a product person, I appreciate any team proposing solutions. My approach is always to objectively explain the pros and cons of each idea to reach the best outcome.

- The first idea reduces the manual steps but still requires human action.
- The second is user-friendly for non-technical users but still not automatic.
- The third idea is the most promising. However, health checks usually only detect if the server is up — they don’t catch if components like the database or Redis are down. Worse, if the health-check endpoint is heavy, this can cause performance degradation and even take the system down due to excess load.

Example: if the system is stable at handling 2 requests, and the health-check adds 1 more, it could overload it. That's why health checks should target lightweight endpoints without logic processing.

![](https://images.viblo.asia/733x529/a4327550-41e0-4829-b6f1-57a096abe6ee.png align="center")

Finally, I proposed using the *Circuit Breaker Pattern* — a design pattern that protects the system from cascading failures.

# III. Circuit Breaker Pattern
## 1. Introduction
The Circuit Breaker Pattern is a design pattern used to manage faults in distributed systems by preventing repeated failed requests to downstream services. When a service is unavailable, the circuit breaker helps the system remain stable and gives the faulty service time to recover.

It helps with:

- Preventing cascading failures
- Stopping requests to problematic services
- Allowing failed services time to recover

## 2. How It Works
The circuit breaker has three states:

- Closed: All requests go through. If failures (timeouts, 500 errors, etc.) reach a threshold, it switches to Open.
- Open: Immediately blocks all requests to the faulty service to prevent overload. After a delay, it switches to Half-Open.
- Half-Open: Sends a limited number of test requests. If they succeed, return to Closed. If not, go back to Open.

![](https://images.viblo.asia/971x642/b452f1ec-a96d-430d-a5c0-f686b6f1e197.jpg align="center")

Analogy: Like a power circuit breaker — Closed means power flows, Open means it's cut.

## 3. Implementation
The backend team used the gobreaker library (by Sony) in Golang to apply the pattern. To better understand it, I also implemented it manually to see how it works — and honestly, it wasn’t too complex once I grasped the logic.

A basic service was built with a circuit breaker wrapper around the API call logic. It tracks state, failure count, timeout intervals, etc., and blocks requests when thresholds are exceeded.

The logic ensures:

- Only valid requests are passed through
- Failed services are given recovery time
- The system avoids unnecessary load and downtime propagation

The result showed that requests were blocked or allowed according to service health and timeouts, demonstrating how the circuit breaker effectively prevented system-wide issues.

Implement a product service that includes an API to retrieve a list of products.
```Go
package main

import (
	"encoding/json"
	"log"
	"net/http"
)

type Product struct {
	ProductName string `json:"product_name"`
	ID          int    `json:"id"`
}

func getProducts(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}

	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusOK)
	err := json.NewEncoder(w).Encode([]*Product{
		{
			ID:          1,
			ProductName: "iPhone 16 Pro Max",
		},
		{
			ID:          2,
			ProductName: "iPhone 8 Plus",
		},
	})
	if err != nil {
		return
	}
}

func main() {
	http.HandleFunc("/products", getProducts)
	log.Fatal(http.ListenAndServe(":3001", nil))
}
```

A main service that clients can call — this service will, in turn, call the product service to retrieve the list of products.
```Go

func (h *Handler) getExample(w http.ResponseWriter, r *http.Request) {
	if r.Method != http.MethodGet {
		http.Error(w, "Method not allowed", http.StatusMethodNotAllowed)
		return
	}

	products, err := h.product()
	if err != nil {
		http.Error(w, "Error", http.StatusInternalServerError)
		return
	}

	w.Header().Set("Content-Type", "application/json")
	w.WriteHeader(http.StatusOK)
	err = json.NewEncoder(w).Encode(products)
	if err != nil {
		return
	}
}

func main() {
	h := &Handler{CircuitBreaker: NewCircuitBreaker(5*time.Second, 5)}
	http.HandleFunc("/example", h.getExample)

	log.Fatal(http.ListenAndServe(":3000", nil))
}
```

Next, I implemented the Circuit Breaker to solve the problem. I defined a struct that includes:

- State: the current state of the circuit breaker
- Wait: the waiting time before transitioning from Open to Half-Open
- Expiry: if the current time exceeds this value, the circuit breaker will attempt to call the failed service to check if it has recovered
- Failure Threshold: the number of allowed failures before switching to the Open state to block further requests
- Failure: the number of failed calls to the service
- Mu: a mutex to ensure atomic operations in some critical sections

```Go
type CircuitBreaker struct {
	State            string
	Wait             time.Duration
	Expiry           int64
	FailureThreshold int64
	Failure          int64
	Mu               sync.Mutex
}

func NewCircuitBreaker(wait time.Duration, failureThreshold int64) *CircuitBreaker {
	return &CircuitBreaker{
		Wait:             wait,
		Mu:               sync.Mutex{},
		State:            Closed,
		FailureThreshold: failureThreshold,
	}
}

const (
	Open     = "open"
	HalfOpen = "half-open"
	Closed   = "closed"
)
```

The Allow function checks whether a request is allowed to proceed to the target service.
```Go
func (cb *CircuitBreaker) Allow() bool {
	if cb.Expiry < time.Now().Unix() && cb.State == Open {
		cb.State = HalfOpen
		return true
	}

	if cb.Expiry >= time.Now().Unix() || cb.State == Open {
		return false
	}

	return true
}
```
Reset the number of failed service calls to zero when transitioning from Closed to Open.
Update the state to Open when the number of failures exceeds the preconfigured threshold.
```Go
func (cb *CircuitBreaker) ResetFailure() {
	cb.Failure = 0
}

func (cb *CircuitBreaker) UpdateState() {
	if cb.Failure > cb.FailureThreshold {
		cb.State = Open
		cb.Expiry = time.Now().Add(cb.Wait).Unix()
		cb.ResetFailure()
	}
}
```
The Execute function handles the Circuit Breaker logic. If the state is Open, it skips calling the service. If the state is Closed or Half-Open, it proceeds with the call. In the Half-Open state, if the call fails, the state switches back to Open and adds a configured wait time before retrying.

```Go
func (cb *CircuitBreaker) Execute(fc func() ([]byte, error)) (body []byte, err error) {
	if !cb.Allow() {
		fmt.Println("circuit breaker is open")
		return nil, fmt.Errorf("circuit breaker is open")
	}

	switch cb.State {
	case Closed:
		fmt.Println("circuit breaker is closed")
		body, err = fc()
		if err != nil {
			cb.Mu.Lock()
			defer cb.Mu.Unlock()

			cb.Failure++
			cb.UpdateState()
			return nil, err
		}

		return body, err
	case HalfOpen:
		fmt.Println("circuit breaker is half-open")
		cb.Mu.Lock()
		defer cb.Mu.Unlock()

		body, err = fc()
		if err != nil {
			cb.State = Open
			cb.Expiry = time.Now().Add(cb.Wait).Unix()
			return nil, err
		}

		cb.State = Closed
		return body, err
	}

	return
}
```
In the part where the product service is called to retrieve the product list, I wrapped the Circuit Breaker's Execute function to call the API.

```Go
func (h *Handler) product() ([]*ProductData, error) {
	body, err := h.CircuitBreaker.Execute(func() ([]byte, error) {
		client := &http.Client{}
		req, err := http.NewRequest(http.MethodGet, "http://0.0.0.0:3001/products", nil)

		if err != nil {
			return nil, err
		}

		res, err := client.Do(req)
		if err != nil {
			return nil, err
		}
		defer func(Body io.ReadCloser) {
			errClose := Body.Close()
			if errClose != nil {
				return
			}
		}(res.Body)

		body, err := io.ReadAll(res.Body)
		if err != nil {
			fmt.Println(err)
			return nil, err
		}

		return body, nil
	})

	if err != nil {
		return []*ProductData{}, nil
	}

	var response []*ProductData
	err = json.Unmarshal(body, &response)
	if err != nil {
		return nil, err
	}

	return response, nil
}
```

Result
```
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is half-open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is open
circuit breaker is half-open
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
circuit breaker is closed
```

# IV. Conclusion
This implementation was done in a single-node service. In a distributed system with multiple nodes, a centralized locking mechanism (Redis, DB, etc.) should be used instead of sync.Mutex.

Our backend team is currently working on a full implementation. Once it’s deployed to production and monitoring results are available, I’ll share a follow-up article!

- Source code available here:
https://github.com/nguyenvantuan2391996/example-code/tree/master/design-pattern/circuit-breaker

**Note**: Today, I am very lazy, so I converted the article from Viet Nam to English by using ChatGPT :))


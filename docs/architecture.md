# Product Architecture Analysis

## Product Choice

* **Product:** Yandex Go
* **Website:** [https://go.yandex.com/](https://go.yandex.com/)
* **Description:** Yandex Go is a "super-app" ecosystem that combines ride-hailing (taxi), food delivery, grocery delivery, and logistics services into a single user interface.

## Main components

![Yandex Go Component Diagram](./diagrams/out/yandex-go/architecture-component/architecture-component.svg)

[Link to PlantUML code](./diagrams/src/yandex-go/architecture-component.puml)

### Component Descriptions

1. **Mobile App (Client):** The primary interface for end-users to request services (taxi, food), track orders in real-time, and manage payment methods.
2. **API Gateway:** The single entry point for all client requests. It handles routing to appropriate microservices, load balancing, and basic security checks.
3. **Auth Service:** Responsible for user authentication and authorization, managing sessions, and issuing security tokens for API access.
4. **Order Service:** Manages the entire lifecycle of an order (creation, status updates, cancellation, completion) and orchestrates data flow between other services.
5. **Dispatch / Matching Engine:** A core component that runs algorithms to find the most suitable driver or courier for a specific order based on location and rating.

## Data flow

![Yandex Go Sequence Diagram](./diagrams/out/yandex-go/architecture-sequence/architecture-sequence.svg)

[Link to PlantUML code](./diagrams/src/yandex-go/architecture-sequence.puml)

### Interaction Description

**Scenario: Booking a Taxi Ride**
This sequence describes the flow when a user requests a ride.

1. The **Mobile App** sends a "Create Order" request to the **API Gateway**.
2. The **API Gateway** forwards the request to the **Order Service**.
3. The **Order Service** validates the request and queries the **Pricing Service** to calculate the estimated fare.
4. Once confirmed, the **Order Service** triggers the **Dispatch Engine** to find a driver.
5. The **Dispatch Engine** notifies the **Driver App**, and upon acceptance, the status is returned through the chain back to the user.

## Deployment

![Yandex Go Deployment Diagram](./diagrams/out/yandex-go/architecture-deployment/architecture-deployment.svg)

[Link to PlantUML code](./diagrams/src/yandex-go/architecture-deployment.puml)

### Deployment Description

The system is designed as a microservices architecture deployed in a cloud environment (likely Yandex Cloud). Services are containerized (e.g., using Docker) and orchestrated by Kubernetes across multiple Availability Zones to ensure high availability and fault tolerance. Static assets are served via a CDN to reduce latency.

## Assumptions

1. I assume the **Pricing Service** uses dynamic "surge" pricing algorithms that calculate costs in real-time based on local demand and supply, rather than using static tables.
2. I assume that **Geo-spatial data** (driver and user positions) is handled by a specialized high-performance database or in-memory cache (like Redis or a custom solution) to handle frequent updates.

## Open questions

1. How does the system handle **network partitions or poor connectivity** for drivers in remote areas to ensure ride state consistency?
2. What specific mechanism is used to **synchronize data** between the different verticals (Taxi, Eats, Market) within the single super-app (e.g., shared user profiles vs. distinct databases)?
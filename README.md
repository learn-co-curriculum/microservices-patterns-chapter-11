## Questions



*   In microservices architecture, who is responsible for authentication and who is responsible for authorization? Some way to centralize these responsibilities? Or is it better to have each service handle its own authentication and authorization requirements?
    *   Is it better to have the API Gateway implement authorization logic for all services? Or for each service to implement its own authorization logic?
    *   When using an transparent token like a JWT, each service needs to be able to decrypt the JWT. What are some secure and efficient ways to give the service awareness of the secret key for decryption?
    *   If API Gateway should not handle authorization in order not to take-on too much awareness of an individual service's business logic, how does OAuth 2.0's usage of a central authorization server avoid this concern?
*   Are there some types of configuration info that are better suited to the push vs. the pull model of configuration?
*   How can we update configuration properties without restarting the application?
*   Which observability patterns do we currently implement? Are there any that we don't implement that could benefit us? 
*   How can we determine what constitutes a "healthy" application for the purposes of a health check endpoint? When should we invoke a health check endpoint? How should the system act on the results of a negative response?
*   How do we think about what to log in a given service or feature that touches multiple services? 
*   How do we think about what metrics to collect in a given service?
*   How do we think about when and how to raise exceptions? 
*   What is "Aspect Oriented Programming"?
*   What is a service mesh? How can one central, external application handle something like logging which needs to happen from an individual service?
*   For the chassis, would ops or someone be responsible for writing our own custom one? Like what if we had some framework that let us spin up microservices quickly? (Is that what Tom Clark was talking about when we last saw him)
*   How great is our ops team? ++ 


## Summary 

**Three Areas of Concern for putting microservice architecture into production:**



1.  Security
1.  Service configurability
1.  Observability 

**Security **



*   Authentication
    *   In-memory session won't work. We need to be able to pass "current user" identity between services. 
    *   API Gateway can handle authentication. 
    *   Downsides of moving this responsibility to the individual services:
        *   Permits unauthenticated requests to enter the internal system 
        *   Relies on each individual service's dev team to coordinate and correctly implement authentication
    *   API Gateway auth:
        *   Gateway implements authentication logic for _all _services. Issues access token that gets included in requests to individual services. Individual service needs to know how to use that token to verify authenticity and get user info. 
*   Authorization
    *   Advantages of API Gateway handling authorization:
        *   Centralize authorization logic
        *   Prevent requests that aren't authorized from even getting to the service
    *   Disadvantages:
        *   Not practical for API Gateway to know the business logic of each service. It would need this info to properly authenticate requests.
    *   Better to have the individual services handle authorization
    *   How to pass user info among services so that each service _can _handle authorization?
        *   Opaque token: Service needs to make a call to a central service to turn token into user info
        *   Transparent token: token includes user info. Ex- JWT 
    *   JWT auth has a downside: no way to revoke token. One options is to make tokens short-lived with an expiration date. But then, how to continuously re-issue valid tokens?
        *   Using OAuth 2.0 
            *   Implements an authorization server that issues a resource-specific token
            *   **Resource token includes a user's roles such that when an individual service gets this token, it can use the roles to make decisions about authorization.** 
*   Auditing
*   Interprocess communication

**Service Configurability**

A service should be built once by a deployment pipelines and deployed into multiple environments. We need to abstract away environment-specific configuration needs among services. 

Supply the correct configuration requirements (ex- secret keys and tokens) with the **Externalized Configuration Pattern. **Uses either the _push _model or _pull _model. 

Push model: deployment infrastructure supplies config info to a service via something like a config file

Pull model: service fetches config info as part of deployment and start up process

Advantages of the pull model:



*   Centralized configuration data
*   Centralize decryption of configuration data
*   Able to "re-pull" while app is running to apply updates

**Designing Observable Services **

As developers of a microservices architecture we are responsible for exposing a service's health, making is possible to track and visualize a service's behavior. Without this, we can't debug and diagnose issues. 

How?



*   Health check api
*   Log aggregation 
*   Distributed tracing
*   Exception tracking
*   Application metrics

**Microservice Chassis Pattern**

A framework of frameworks to handle the standard microservices concerns discussed in this chapter, so you don't have to do it all from scratch, allowing you to focus on writing the business logic.

Service mesh
<p class='util--hide'>View <a href='https://learn.co/lessons/microservices-patterns-chapter-11'>Microservices Patterns Chapter 11</a> on Learn.co and start learning to code for free.</p>

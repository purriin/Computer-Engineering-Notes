### 1. Definition
**REST**: Representational State Transfer
- Standardized software architecture style
- All about communication between web and server
- "Restful" web service is a service that uses REST API

### 2. Benefits of REST APIs
1. Simple and standardized approach to communication
2. Scalable and stateless
	1. As service grows in complexity can easily make edits
	2. Don't need to keep track of the state of the data or the web and server
	3. High performance and allows caching

### 3. Implementation
1. **Define an endpoint**
	- Ex. `http://icecream.com/api/flavors`
		- `api` signifies the API portion of the endpoint
		- `flavors` is known as a "resource"
2. **Request to the server**
	- **CRUD**: Create, Read, Update, and Delete
		- HTTP methods or operations 

| **CRUD** | **HTTP Method** |
| -------- | --------------- |
| Create   | POST            |
| Read     | GET             |
| Update   | PUT             |
| Delete   | DELETE          |

- Composed of:
	1. Header
	2. Operation
	3. Endpoint
	4. Parameters/Body

3. **Response from the server**
	- Typically in the form of JSON
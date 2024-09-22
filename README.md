# Blog Platform

## Overview

The Blog Platform is a full-stack application consisting of a Django REST Framework (DRF) backend, a Vue.js frontend, and a FastAPI service for managing comments. This project provides a comprehensive blogging experience with user authentication, blog post management, and real-time commenting functionality.

## Features

- **User Registration and Authentication**: Users can register and log in using JWT.
- **Blog Management**: Create, read, update, and delete blog posts.
- **Comment System**: Users can add comments to blog posts, managed by a FastAPI service.
- **Caching**: Utilizes Redis for caching blog posts and comments to optimize performance.

## Database Information

- **Django (DRF)**: Uses **PostgreSQL** as the database.
- **FastAPI**: Uses **MySQL** as the database.
- **Redis**: Both services use Redis for caching.

## Setup Instructions for Running the Project Using Docker Compose

### Prerequisites

- Ensure you have [Docker](https://www.docker.com/products/docker-desktop) and [Docker Compose](https://docs.docker.com/compose/) installed on your machine.

### Steps to Set Up

1. **Clone the Repository**:
   ```bash
   git clone https://github.com/LAMSADDEQALAA/blog-platform
   cd blog-platform

2. **Build the Docker Images**:
    ```bash
    docker-compose build

3. **Run the Docker Containers**:
    ```bash
    docker-compose up

4. **Stopping the Services**:
    ```bash
    docker-compose down

## Access the Services:

- **Django (DRF) API**: http://localhost:8000/api/
- **Vue.js Frontend**: http://localhost:8080/
- **FastAPI Comments Service**: http://localhost:8001/api/

## Architecture Overview

### Advantages of the Project Architecture

The architecture of this project, which includes a Django (DRF) core service, a Vue.js frontend, and a FastAPI comments service, offers several key advantages:

1. **Separation of Concerns**: Each service is responsible for distinct functionalities—Django handles user authentication and blog management, FastAPI manages comments, and Vue.js provides the user interface. This separation makes the codebase easier to maintain and enhances scalability.

2. **Flexibility in Technology Stack**: By utilizing different frameworks (Django, FastAPI, and Vue.js), the architecture leverages the strengths of each technology. Django is well-suited for building robust REST APIs with extensive features, FastAPI excels in performance and asynchronous capabilities, and Vue.js offers a reactive and user-friendly frontend.

3. **Improved Performance**: Using FastAPI for the comments service allows for high-performance, asynchronous handling of requests, which is beneficial for real-time interactions. The separation of the comments service from the core service ensures that comment-related operations do not impact the performance of the main application.

4. **Scalability**: The architecture allows each service to scale independently based on demand. For instance, if the comments service experiences high traffic, it can be scaled up without affecting the core service. This scalability is facilitated by containerization with Docker, enabling easy deployment and orchestration.

7. **Modular Development**: The architecture promotes modular development, allowing teams to work on different services simultaneously without causing conflicts. This modularity accelerates development and facilitates easier integration of new features or services in the future.

8. **Easier Testing and Debugging**: Each service can be tested and debugged independently. This isolation simplifies the identification of issues and ensures that changes in one service do not inadvertently affect others.

In summary, this project architecture enhances maintainability, performance, scalability, and collaboration, making it a robust foundation for building a comprehensive blog platform.

### Service Communication

In this architecture, the **Core Service** (Django DRF) and **Comments Service** (FastAPI) do not communicate directly. Instead, the comment model includes a `user_id`, which refers to the user in the Core Service. This user information is mapped on the frontend.

### Rationale for This Approach

1. **Decoupling Services**: By avoiding direct communication between the two services, we achieve a cleaner separation of concerns. Each service can evolve independently without affecting the other, promoting better maintainability and scalability.

2. **Frontend Mapping**: Mapping the `user_id` on the frontend allows for a more flexible user experience. The frontend can easily handle user data without requiring real-time communication between services, reducing latency and improving performance.

3. **Simplified Logic**: This approach minimizes the complexity of managing cross-service interactions, especially for operations like fetching comments that may involve multiple user queries. It allows each service to focus on its primary responsibilities without additional overhead.

4. **Improved Performance**: By eliminating unnecessary API calls between services, we reduce the load on the backend and improve response times, resulting in a smoother user experience.

By adopting this architecture, we ensure that both services can be developed and deployed efficiently while providing a seamless experience for users interacting with comments in blog posts.

### Why Redis Instead of Local Caching?

Using Redis for caching in both the FastAPI and Django REST Framework (DRF) services offers several advantages over local caching mechanisms:

1. **Centralized Caching**: Redis acts as a centralized caching layer that can be accessed by multiple services. This allows both the FastAPI comments service and the Django core service to share cached data seamlessly. In contrast, local caching would be confined to individual services, making it harder to maintain consistency across them.

2. **Scalability**: Redis is designed to handle a large volume of data and high throughput. As the application scales and more instances of services are deployed, Redis can efficiently manage increased caching demands. Local caching, however, would be limited by the resources of each individual service instance.

6. **Enhanced Performance**: By storing frequently accessed data in memory, Redis significantly reduces latency for read operations, providing a faster response time for users. Local caching, while beneficial for individual services, may not offer the same level of performance across distributed systems.

In summary, using Redis for caching in both the FastAPI and DRF services enhances the overall architecture by improving scalability, performance, and data consistency while simplifying cache management across services.

## Caching Strategy: Benefits of Query Parameter-Based Caching

Caching based on query parameters for search and pagination offers several advantages:

1. **Improved Performance**: By caching responses specific to query parameters, we reduce the need for repeated database queries. This significantly speeds up response times for frequently accessed data.

2. **Reduced Load on Backend**: Caching minimizes the number of requests hitting the backend services. This is particularly beneficial for high-traffic applications where the same queries are made repeatedly.

3. **Efficient Resource Utilization**: Query parameter-based caching optimizes the use of memory and storage by only caching relevant data sets, rather than caching entire datasets that may not be useful for all requests.

4. **Dynamic Results Handling**: When implementing search and pagination, results can change frequently. By caching based on query parameters, we ensure that users receive the most relevant and up-to-date data tailored to their specific queries.

5. **Improved User Experience**: Faster response times and reduced latency lead to a smoother user experience, particularly when navigating through large datasets or performing complex searches.


## API Documentation

### Comments Service (FastAPI)

- **Base URL**: `/api`

#### Endpoints

- **Get Comments for a Post**
  - **Endpoint**: `GET /posts/{post_id}/comments`
  - **Description**: Retrieve all comments for a specific post.
  - **Parameters**:
    - `post_id` (int): The ID of the post for which to retrieve comments.
  - **Responses**:
    - **200 OK**: Returns a list of comments.
      ```json
      [
          {
              "id": 1,
              "content": "This is a comment.",
              "user_id": 123,
              "post_id": 456
          },
          {
              "id": 2,
              "content": "Another comment.",
              "user_id": 124,
              "post_id": 456
          }
      ]
      ```
    - **401 Unauthorized**: If the user is not authenticated.

- **Create a Comment**
  - **Endpoint**: `POST /comments`
  - **Description**: Add a new comment.
  - **Request Body**: 
    - `CommentCreate` schema:
      ```json
      {
          "content": "This is a new comment.",
          "user_id": 123,
          "post_id": 456
      }
      ```
  - **Responses**:
    - **201 Created**: Returns the created comment.
      ```json
      {
          "id": 3,
          "content": "This is a new comment.",
          "user_id": 123,
          "post_id": 456
      }
      ```
    - **401 Unauthorized**: If the user is not authenticated.

- **Update a Comment**
  - **Endpoint**: `PUT /comments/{comment_id}`
  - **Description**: Update an existing comment.
  - **Parameters**:
    - `comment_id` (int): The ID of the comment to update.
  - **Request Body**: 
    - `CommentUpdate` schema:
      ```json
      {
          "content": "Updated comment content."
      }
      ```
  - **Responses**:
    - **200 OK**: Returns the updated comment.
      ```json
      {
          "id": 1,
          "content": "Updated comment content.",
          "user_id": 123,
          "post_id": 456
      }
      ```
    - **404 Not Found**: If the comment does not exist.
    - **401 Unauthorized**: If the user is not authenticated.

- **Delete a Comment**
  - **Endpoint**: `DELETE /comments/{comment_id}`
  - **Description**: Delete a comment.
  - **Parameters**:
    - `comment_id` (int): The ID of the comment to delete.
  - **Responses**:
    - **204 No Content**: Successfully deleted the comment.
    - **404 Not Found**: If the comment does not exist.
    - **401 Unauthorized**: If the user is not authenticated.

## API Documentation

### Core Service (Django DRF)

- **Base URL**: `/api/users`

#### User Endpoints

- **User Registration**
  - **Endpoint**: `POST /register/`
  - **Description**: Register a new user.
  - **Request Body**: 
    - `UserCreateUpdateSerializer` schema:
      ```json
      {
          "username": "newuser",
          "password": "password123",
      }
      ```
  - **Responses**:
    - **201 Created**: Returns the created user's ID and username.
      ```json
      {
          "id": 1,
          "username": "newuser"
      }
      ```
    - **400 Bad Request**: If the input data is invalid.

- **Get User Profile**
  - **Endpoint**: `GET /profile/`
  - **Description**: Retrieve the authenticated user's profile.
  - **Responses**:
    - **200 OK**: Returns the user profile.
      ```json
      {
          "id": 1,
          "username": "existinguser",
      }
      ```
    - **401 Unauthorized**: If the user is not authenticated.

- **Get Users by IDs**
  - **Endpoint**: `GET /?ids=1,2,3`
  - **Description**: Retrieve user details for specified user IDs.
  - **Parameters**:
    - `ids` (string): Comma-separated list of user IDs.
  - **Responses**:
    - **200 OK**: Returns a list of user details.
      ```json
      [
          {
              "id": 1,
              "username": "user1"
          },
          {
              "id": 2,
              "username": "user2"
          }
      ]
      ```
    - **404 Not Found**: If no users are found for the provided IDs.

- **Obtain Token**
  - **Endpoint**: `POST /token/`
  - **Description**: Obtain access and refresh tokens.
  - **Request Body**: 
    ```json
    {
        "username": "existinguser",
        "password": "password123"
    }
    ```
  - **Responses**:
    - **200 OK**: Returns access and refresh tokens.
      ```json
      {
          "access": "eyJ...",
          "refresh": "eyJ..."
      }
      ```
    - **401 Unauthorized**: If the credentials are invalid.

- **Refresh Token**
  - **Endpoint**: `POST /token/refresh/`
  - **Description**: Refresh the access token.
  - **Request Body**: 
    ```json
    {
        "refresh": "eyJ..."
    }
    ```
  - **Responses**:
    - **200 OK**: Returns a new access token.
      ```json
      {
          "access": "eyJ..."
      }
      ```
    - **401 Unauthorized**: If the refresh token is invalid.

### Blog Post Endpoints

- **Blog Posts**
  - **Endpoint**: `/api/BlogPosts/`
  - **Description**: Manage blog posts.
  
#### Blog Post Operations

- **List Blog Posts**
  - **Method**: `GET`
  - **Parameters**: Supports search using query parameters (`title`, `content`).
  - **Responses**:
    - **200 OK**: Returns a list of blog posts.
      ```json
      [
          {
              "id": 1,
              "title": "First Blog Post",
              "content": "This is the content.",
              "author": 1
          },
          {
              "id": 2,
              "title": "Second Blog Post",
              "content": "This is another content.",
              "author": 2
          }
      ]
      ```
    - **204 No Content**: If no posts are available.

- **Create Blog Post**
  - **Method**: `POST`
  - **Request Body**: 
    - `BlogPostCreateUpdateSerializer` schema:
      ```json
      {
          "title": "New Blog Post",
          "content": "Content for the new blog post."
      }
      ```
  - **Responses**:
    - **201 Created**: Returns the created blog post.
      ```json
      {
          "id": 3,
          "title": "New Blog Post",
          "content": "Content for the new blog post.",
          "author": 1
      }
      ```
    - **401 Unauthorized**: If the user is not authenticated.

- **Update Blog Post**
  - **Method**: `PUT`
  - **Endpoint**: `/api/BlogPosts/{post_id}/`
  - **Request Body**: 
    - `BlogPostCreateUpdateSerializer` schema.
  - **Responses**:
    - **200 OK**: Returns the updated blog post.
    - **404 Not Found**: If the post does not exist.
    - **401 Unauthorized**: If the user is not authenticated.

- **Delete Blog Post**
  - **Method**: `DELETE`
  - **Endpoint**: `/api/BlogPosts/{post_id}/`
  - **Responses**:
    - **204 No Content**: Successfully deleted the blog post.
    - **404 Not Found**: If the post does not exist.
    - **401 Unauthorized**: If the user is not authenticated.

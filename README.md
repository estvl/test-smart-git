#### Build and Run with Docker Compose

1. **Localhost**
    **1.1 Prerequisites**
    
    ### Running Redis as a Docker Container
    
    For the development process in a localhost environment, you can install and run Redis as a Docker container to simplify the setup. Redis is a lightweight, high-performance in-memory database used for caching or 
    messaging.
    
    ```sh
    docker pull redis
    docker run --name my-redis -d redis

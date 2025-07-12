# Deploying-and-Managing-Microservices-in-a-Cloud-Native-Environment

Objective:
Learn how to:
 Set up and configure a Kubernetes cluster
 Containerize and deploy a microservices application
 Manage Kubernetes deployments, services, autoscaling, and persistent 
storage

1. Kubernetes Setup
   We have installed and used Kubernetes locally to deploy and manage our containerized application.

<img width="960" height="540" alt="Screenshot 2025-07-11 191618" src="https://github.com/user-attachments/assets/402082bf-c769-42d4-bb72-04f8be9fa0e2" />


2. Containerization and Docker

We built a sample microservices application consisting of services like watch, upload, nginx, and a MySQL database. For each service, we created separate Dockerfiles, built individual Docker images, and pushed them to Docker Hub for containerized deployment.

<img width="960" height="540" alt="Screenshot 2025-07-11 195942" src="https://github.com/user-attachments/assets/3c810c6f-0de8-48a7-9e84-5d9a19ec656e" />

The Docker images successfully pushed to Docker Hub and is now available for deployment from the container registry.

<img width="960" height="540" alt="Screenshot 2025-07-12 093353" src="https://github.com/user-attachments/assets/883fabb8-0330-464e-a734-4866a13ccc00" />


3. Kubernetes Deployments
   project includes multiple services: the watch service (for displaying uploaded images), the upload service (for handling image uploads), a MySQL database, and NGINX as a reverse proxy.


<img width="960" height="540" alt="Screenshot 2025-07-12 134949" src="https://github.com/user-attachments/assets/10cf02de-be4a-4366-baa6-d258d900460c" />


4. Service Discovery and Load Balancing
   
   In our project, we created Kubernetes Service resources to expose each microservice appropriately. The watch, upload, and MySQL database services were exposed internally using ClusterIP for seamless inter-service communication within the cluster. The NGINX service, acting as a reverse proxy, was exposed externally using a NodePort to allow users to access the application through a browser. 


<img width="960" height="540" alt="Screenshot 2025-07-12 135721" src="https://github.com/user-attachments/assets/522cb24c-5273-4351-8c4f-44e050d4c9a5" />

We verified inter-service communication by checking DNS resolution and pod logs, and confirmed external access by successfully accessing the NGINX endpoint in the browser.


<img width="960" height="540" alt="Screenshot 2025-07-12 135430" src="https://github.com/user-attachments/assets/32cf99de-ed08-4b2a-b881-bd00e548b023" />

5. Scaling

In our project, we configured the Horizontal Pod Autoscaler (HPA) for key microservices including NGINX, upload, and watch, to enable dynamic scaling based on CPU usage. For each service, we set a target CPU utilization threshold (e.g., 50%) to maintain performance under varying load. To simulate high traffic, we generated artificial load using tools like curl in a loop from within the cluster. We observed the autoscaling behavior through kubectl get hpa, confirming that each service scaled its pod replicas automatically in response to increased resource consumption.

<img width="960" height="540" alt="Screenshot 2025-07-12 135721" src="https://github.com/user-attachments/assets/7581b5a0-a6c5-4665-894f-1dca3ddcd675" />

To verify that autoscaling was working in our project, we monitored the Horizontal Pod Autoscaler using kubectl get hpa, observed the increase in pod replicas when CPU usage crossed the defined threshold, and confirmed that the HPA automatically scaled the affected microservices (like NGINX, upload, and watch) based on the generated load.

<img width="960" height="540" alt="Screenshot 2025-07-12 143127" src="https://github.com/user-attachments/assets/2215af06-f633-4f3c-acd5-680eb392931c" />









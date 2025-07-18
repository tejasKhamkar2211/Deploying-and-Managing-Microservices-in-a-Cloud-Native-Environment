# Deploying-and-Managing-Microservices-in-a-Cloud-Native-Environment

Objective:
Learn how to:
 Set up and configure a Kubernetes cluster
 Containerize and deploy a microservices application
 Manage Kubernetes deployments, services, autoscaling, and persistent 
storage

🌿 NatureNest Application Flow
a) User sends a request
The user opens the browser and accesses the application through the Nginx reverse proxy via the NodePort (e.g., http://<EC2-IP>:30080).

b) Request reaches Nginx (Frontend Service)
The Nginx container receives the HTTP request and forwards it to the respective internal microservice (upload or watch) using their internal ClusterIP services.

Nginx forwards request to Upload or Watch microservice

If the user is uploading an image, the request goes to the Upload Service (upload pod on port 5000).

If the user wants to view images, the request goes to the Watch Service (watch pod on port 5001).

c) Upload Service Logic

Receives image and metadata from user.

Stores the image in the S3 bucket (e.g., tejas-new-image-bucket).

Stores the image URL and timestamp in the MySQL database (table: images).

Saves metadata to the /shared_storage volume temparary.

d) Watch Service Logic

Receives request to show uploaded images.

Queries the MySQL database for image URLs and timestamps.

Displays the image gallery using the data retrieved from MySQL.

e) Database (MySQL)

Running in a pod with a Persistent Volume Claim (PVC).

Stores structured data like image id, URL, and uploaded_at timestamp.

Ensures data is not lost even after pod restarts, thanks to PV/PVC.




f) Response is returned
Data flows back through:
→ Watch/Upload Service
→ Nginx
→ User's browser (as image upload confirmation or image gallery)

![Slide1](https://github.com/user-attachments/assets/7002b1de-c552-4542-a033-dacf9993de2b)

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

<img width="960" height="540" alt="Screenshot 2025-07-12 152434" src="https://github.com/user-attachments/assets/1d708137-d1f6-43d3-a8bc-d433a79f3cf1" />


To verify that autoscaling was working in our project, we monitored the Horizontal Pod Autoscaler using kubectl get hpa, observed the increase in pod replicas when CPU usage crossed the defined threshold, and confirmed that the HPA automatically scaled the affected microservices (like NGINX, upload, and watch) based on the generated load.

<img width="960" height="540" alt="Screenshot 2025-07-12 143127" src="https://github.com/user-attachments/assets/2215af06-f633-4f3c-acd5-680eb392931c" />


In our project, we implemented persistent storage by creating a Persistent Volume (PV) and a Persistent Volume Claim (PVC) specifically for the MySQL database. We modified the database configuration to use this volume for storing its data. This ensures that the upload and watch microservices can consistently read and write image metadata to the database. We verified persistence by inserting and retrieving data through the services, and confirmed that the data remains intact even after pod restarts.


<img width="960" height="540" alt="Screenshot 2025-07-12 153141" src="https://github.com/user-attachments/assets/de43252f-7565-4b94-ab43-e7f63df82098" />


<img width="960" height="540" alt="Screenshot 2025-07-12 155109" src="https://github.com/user-attachments/assets/1385283a-bdaf-4f91-ab6b-09a26da963f3" />






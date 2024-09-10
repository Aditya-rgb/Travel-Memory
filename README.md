
# Travel Memory Application Deployment with AWS Services

## Overview

This document outlines the steps to deploy the Travel Memory application using AWS services. The deployment includes:

- Hosting the application on an EC2 instance.
- Creating multiple instances of the application.
- Integrating Nginx to serve the application on port 80.
- Attaching an AWS Load Balancer and setting up a domain on Cloudflare.

## Setting up the Application on AWS EC2 Instance

1. **Launch EC2 Instance**: 
   - Cloned the Travel Memory repository, which is divided into Frontend and Backend.
   ```bash
   sudo apt-get update
   sudo apt install nodejs
   node --version
   git clone https://github.com/UnpredictablePrashant/TravelMemory.git
   cd TravelMemory/
## Connecting the Application to MongoDB

### Steps to Set Up MongoDB on Atlas

1. **Log in to MongoDB Atlas**:
   - Access the MongoDB Atlas account.

2. **Create Organization and Project**:
   - Create an organization and a project within the MongoDB Atlas dashboard.

3. **Create a Cluster**:
   - Click on **Create Cluster**.
   - Name the cluster `cluster0` and select the **M0 Free Plan**.
   - Click **Create Deployment** to deploy the cluster.

4. **Create a Database User**:
   - Set up the database user by specifying a **username** and **password**.
   - Click **Create DB User** to finalize the creation.

5. **Configure Network Access**:
   - Go to the **Network Access** section from the left panel.
   - Click **Edit** and set the IP address access to `0.0.0.0/0` to allow access from all IP addresses.
   - Confirm the changes.

6. **Connect MongoDB to Compass**:
   - Go to the **Database** section in the left panel and click **Connect**.
   - Select **Compass** as the connection method.
   - Choose **I already have MongoDB Compass** if it's installed on your local machine.

7. **Copy the Connection String**:
   - Copy the connection string from the MongoDB Atlas interface.

8. **Connect via MongoDB Compass**:
   - Open the MongoDB Compass application on your desktop.
   - Paste the copied connection string into the **New Connection** field in Compass.
   - Click **Connect** to establish the connection with MongoDB Atlas.


## Connecting the Backend Code to MongoDB and Hosting the Application

### Steps to Connect the Backend to MongoDB

1. **Navigate to the Backend Directory**:
   After cloning the Travel Memory application repository on your EC2 instance, navigate to the backend directory:
   ```bash
   cd /home/ubuntu/TravelMemory/
   cd Backend
   sudo touch .env # Created an environment file to store sensitive information such as the MongoDB URI and port number:
   nano .env
   #Pasted the below lines inside .env file
   #MONGO_URI='mongodb+srv://adityavakharia:Alphavak98@cluster0.7powl.mongodb.net/Travel-Memory-DB'
   #PORT=3001
   sudo apt install npm # Installed NPM and Dependencies and installed the necessary Node.js dependencies:
   npm install
   node index.js # Start the backend server
   ```
   Output on the EC2 inside : Server started at http://localhost:3001
   Now, went back to AWS console, when to my instance, went to "Security", went inside my security, clicked on edit inbound rules and added the below port details:
   ```bash
   custom-TCP TCP 3001 Custom 0.0.0.0/0
   ```
     
   Copied the public IP Address of the EC2 instance, went to the browser and pasted the below url :
   ```bash
   http://35.95.79.227:3001/
   ```

   ## Establishing the Connection Between the Frontend and Backend Code Bases

1. Opened a new terminal on the same EC2 instance:
    ```bash
    cd /home/ubuntu/TravelMemory/
    cd Frontend
    cd src
    nano url.js
    ```

2. Added the following lines to `url.js`:
    ```javascript
    export const baseUrl = process.env.REACT_APP_BACKEND_URL || "http://35.95.79.227:3001";
    ```

3. Started the frontend application:
    ```bash
    cd ..
    npm start
    ```
    The public IP address of the EC2 instance where the backend application is hosted was pasted in the `url.js` file.

4. Updated Security Group Rules:
    - Went to the AWS console, navigated to the instance, then to "Security".
    - Edited the inbound rules and added a new rule for port 3000.

5. Verified the setup by pasting the following IP address into the browser:
    ```plaintext
    http://35.95.79.227:3000/
    ```

## Creating a Reverse Proxy Using Nginx for the Travel Memory Application

1. Installed and set up Nginx on the EC2 instance:
    ```bash
    sudo apt-get update
    sudo apt-get install nginx
    ```

2. Edited the Nginx configuration:
    ```bash
    cd /etc/nginx/sites-enabled
    sudo nano default
    ```

3. Commented out the default configuration lines:
    ```nginx
    #root /var/www/html;
    #index index.html index.htm index.nginx-debian.html;
    #index index.html index.htm index.nginx-debian.html;
    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        #try_files $uri $uri/ =404;
    }
    ```

4. Added the following configuration to proxy requests:
    ```nginx
    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        #try_files $uri $uri/ =404;
        proxy_pass http://35.95.79.227:3000;  # Public IP address of the EC2 instance where the application is hosted.
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    ```

5. Restarted and tested Nginx:
    ```bash
    sudo service nginx start
    sudo service nginx reload
    sudo nginx -t
    ```

6. Tested the reverse proxy by navigating to the following URL in the browser:
    ```plaintext
    http://35.95.79.227:80
    ```


    ## Creating multiple instances of both the frontend and backend servers.

1. **Created a Launch Template from the Existing EC2 Instance:**
    - Navigated to the existing EC2 instance created for the Travel Memory application.
    - Went to `Actions` -> `Images & Templates` -> `Create template from instance`.
    - Provided the name of the template as `Aditya-TM-Template`.
    - Clicked on `Create launch template`.

2. **Launched Instances from the Template:**
    - Went to `Actions` -> `Launch instance from template`.
    - Selected the source template name.
    - Clicked on `Launch instance`.
    - In the AWS console, verified that two instances now exist for the Travel Memory application under the same instance name:
        - `Aditya-TM: 35.87.195.45` (2nd server made from template)
        - `Aditya-TM: 35.95.79.227` (1st server)

## Creating and attaching the Load Balancer to the 2 instances I created for our travel memory aplication in AWS.

1. **Configured the Load Balancer:**
    - Navigated to `Load Balancers` in the AWS GUI.
    - Selected `ALB (Application Load Balancer)`.
    - Set the load balancer name as `Aditya-TM-LB`.
    - Selected the scheme `internet-facing`.
    - Selected the load balancer IP address type as `IPv4`.
    - For setting the availability zones, selected two AZs matching the AZs of the EC2 instances.

2. **Created a Target Group:**
    - Under `Listeners and routing`, created a Target Group to include all instances running the Travel Memory application.
    - Clicked on `Create a target group`.
    - Selected the instances to be added to the target group.
    - Set the port for routing traffic to the selected instances as `80` and clicked on `Include as pending`.
    - Clicked on `Create Target Group`.

3. **Created the Load Balancer:**
    - Returned to `Load Balancers` and clicked on `Create Load Balancer`.

4. **Tested the Load Balancer:**
    - Copied the DNS name of the load balancer (`Aditya-TM-LB-1185255078.us-west-2.elb.amazonaws.com`) and pasted it into the browser.
    - Verified that the Travel Memory application was working.


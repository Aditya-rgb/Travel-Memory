
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

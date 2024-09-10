
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

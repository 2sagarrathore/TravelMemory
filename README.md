# Travel Memory

A full-stack MERN travel journal application. This fork extends the original [UnpredictablePrashant/TravelMemory](https://github.com/UnpredictablePrashant/TravelMemory) with a production-style AWS deployment.

---

## Deployment

The application runs across two AWS EC2 instances behind an Application Load Balancer, with MongoDB Atlas as the database layer and Cloudflare DNS for the custom domain.

### Architecture

```
User
 └──> Cloudflare DNS (saagarsaga.store)
       └──> AWS Application Load Balancer (ap-south-1)
             ├──> EC2 Instance 1 (Ubuntu 24.04, t2.micro)
             │     ├─ Backend  : Node.js + Express, port 3000 (PM2)
             │     ├─ Frontend : React build served by Nginx, port 3001
             │     └─ Reverse proxy: Nginx on port 80 -> 3000
             │
             └──> EC2 Instance 2 (Ubuntu 24.04, t2.micro)
                   ├─ Backend : Node.js + Express, port 3000 (PM2)
                   └─ Reverse proxy: Nginx on port 80 -> 3000

                                    │
                                    ▼
                       MongoDB Atlas (M0, ap-south-1)
```

### Stack

| Layer            | Technology                             |
| ---------------- | -------------------------------------- |
| Frontend         | React (Create React App), built static |
| Backend          | Node.js, Express                       |
| Database         | MongoDB Atlas                          |
| Process manager  | PM2                                    |
| Web server       | Nginx (reverse proxy + static serving) |
| Compute          | AWS EC2 (t2.micro, Ubuntu 24.04)       |
| Load balancer    | AWS Application Load Balancer          |
| DNS              | Cloudflare (proxied)                   |
| Domain registrar | Namecheap (`saagarsaga.store`)         |

### Live endpoints

| Endpoint                   | URL                                                          |
| -------------------------- | ------------------------------------------------------------ |
| Frontend (EC2 Instance 1)  | `http://13.201.222.34:3001`                                  |
| Backend (Load Balancer)    | `http://travelmemory-alb-1189099262.ap-south-1.elb.amazonaws.com` |
| Custom domain              | `saagarsaga.store` (proxied via Cloudflare)                  |

### Documentation

A full step-by-step deployment guide with screenshots and the architecture diagram is available as `TravelMemory-Deployment-Guide.pdf`. It covers MongoDB Atlas setup, EC2 provisioning, security group configuration, SSH/PM2/Nginx setup, frontend build, second-instance setup, load balancer creation, target group registration, and Cloudflare DNS + Namecheap nameserver configuration.

### Notable changes in this fork

- Documented the AWS architecture and stack in this README.
- Configured the frontend `src/url.js` to point at the deployed backend at build time on each EC2 instance.
- Set up backend environment variables (`MONGO_URI`, `PORT=3000`) on each EC2 instance, kept out of version control.
- Configured Nginx as a reverse proxy from port 80 to the Node backend on port 3000, and a separate server block to serve the React static build on port 3001.
- Used PM2 to run the Node backend as a managed process with crash-restart on both instances behind the load balancer.
- Set up Cloudflare DNS records (A and CNAME) to point the custom domain at the EC2 frontend and the load balancer respectively.

---

## Local Development

### Backend

Create a `.env` file in `backend/` with your MongoDB connection string:

```
MONGO_URI='ENTER_YOUR_URL'
PORT=3001
```

Then:

```bash
cd backend
npm install
npm start
```

### Frontend

Create a `.env` file in `frontend/` with the backend URL:

```bash
REACT_APP_BACKEND_URL=http://localhost:3001
```

Then:

```bash
cd frontend
npm install
npm start
```

### Sample document shape

```json
{
    "tripName": "Incredible India",
    "startDateOfJourney": "19-03-2022",
    "endDateOfJourney": "27-03-2022",
    "nameOfHotels": "Hotel Namaste, Backpackers Club",
    "placesVisited": "Delhi, Kolkata, Chennai, Mumbai",
    "totalCost": 800000,
    "tripType": "leisure",
    "experience": "Lorem Ipsum, Lorem Ipsum, ...",
    "image": "https://example.com/some-image.jpg",
    "shortDescription": "India is a wonderful country with rich culture and good people.",
    "featured": true
}
```

---

Maintained by [@2sagarrathore](https://github.com/2sagarrathore).

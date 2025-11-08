# System Architecture: Dimension Five (The Interior Design Planner)

This document outlines the technical blueprint of Dimension Five - Planner 5D, focusing on high-performance 3D visualization, robust data integrity, and real-time cost analysis.


![IMG-20251108-WA0091](https://github.com/user-attachments/assets/9f1f6837-b785-49a5-afeb-f7eb87d5ed5a)


## Technical Stack Overview

Dimension Five utilizes a **Microservice Architecture** to isolate the computationally intensive 3D rendering from the core scheduling/financial services, and other components, ensuring performance remains fast.

| Component | Technology | Rationale |
| :--- | :--- | :--- |
| **Frontend & 3D Rendering** | **React (Web/Mobile)** with **Three.js** or **Babylon.js** | **3D Visualization**: Handles client-side rendering of complex room models. Offloads intensive graphical processing from the server to the user's device, ensuring fast, interactive design manipulation. |
| **Core API (5D Logic)** | **Python (Django Rest Framework)** | **Business Logic & Finance**: Manages complex calculations linking design items to budgets. Chosen for Python's stability and robust libraries for security and data integrity (crucial for financial data). |
| **3D Asset Microservice** | **Node.js/Express** | **High-Volume I/O**: A dedicated, lightweight service for storing, retrieving, and optimizing large 3D model files and material textures. Isolates this high-bandwidth task from the core financial API. |
| **Database** | **PostgreSQL** | **Data Integrity**: Used for all transactional data (user accounts, budget line items, scheduling, item metadata). Its ACID compliance is vital for reliable financial and project record-keeping. |
| **Cloud Hosting & Storage** | **AWS (ECS, S3, RDS)** | **Scalability & Reliability**: Utilizes Amazon S3 for economical storage of static assets (3D models) and ECS/RDS for horizontal scaling and reliability of the application and database tiers. |
| **Real-time Communication** | **WebSockets** (via Django Channels) | **Collaboration & Alerts**: Facilitates instant communication between the server and all connected users, enabling real-time collaboration and immediate budget/schedule alerts. |
| **Authentication** | **JWT (JSON Web Tokens)** via OAuth 2.0 | **Security**: Provides a secure, stateless method for user identity verification and authorization across both the Core API and the 3D Asset Service. |

## Component Communication

3D Visualization Flow (Key Change):
- The Frontend requests model data from the 3D Asset Service (Node.js).
- The 3D Asset Service retrieves the raw model data from S3, optimizes it, and serves it to the browser for rendering by Three.js.

5D Cost Logic Flow:
- When a user adds or changes an item in the 3D model, the Frontend sends a REST request to the Core API (Django). 
- The Core API validates the change, updates the PostgreSQL database with the new item/cost, and runs the budget projection.
- Real-time updates (budget alerts, collaboration) are pushed via WebSockets (managed by the Core API) to all connected clients.

Catalog Integration: 
- The Core API includes endpoints for safely ingesting and normalizing product/cost data from external vendor APIs or file uploads, mapping those costs to internal 3D assets.


## Technical Feasibility Rationale

The architecture is technically robust because:

1. Performance: Separating the 3D Asset Service ensures the main Core API isn't burdened by high-bandwidth file transfer, keeping budget calculations fast. Using client-side rendering with Three.js offloads the most intensive processing to the user's device.

2. Data Accuracy: PostgreSQL maintains the required data integrity for linking financial records (cost/budget) directly to geometric data (the 3D model), preventing errors that could lead to physical project mistakes.

3. Maintainability: The microservice approach allows the design and engineering teams to independently update and scale the specialized 3D logic and the core financial logic.



![IMG-20251108-WA0093](https://github.com/user-attachments/assets/11d8aa65-9b38-422d-8bb2-670ca26fda93)

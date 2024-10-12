---
layout: default
title: Kai - High Level Solution Design
description: Detailed overview of Kai's architecture and components
nav_order: 2
permalink: /high-level-design
last_modified_date: 2024-10-12
---

# Kai: High Level Solution Design

This document provides a comprehensive overview of Kai's architecture, detailing each layer and its components. The entire system is containerized using Docker for easy deployment and scalability.

## Architecture Overview

Kai's architecture is divided into four main layers:

1. Data Layer
2. LLM (Language Model) Layer
3. Orchestration Layer
4. User Interface Layer

Each layer serves a specific purpose and interacts with the others to create a cohesive, efficient KYC AI investigation system.

## 1. Data Layer

The Data Layer is responsible for data generation, event streaming, and storage. It consists of three main components:

### 1.1 Shadowtraffic
- **Purpose**: Synthetic Data Generation
- **Description**: Shadowtraffic generates realistic, synthetic data that mimics real-world KYC scenarios. This allows for comprehensive demo, testing and training of the system without using sensitive customer data.

### 1.2 Kafka
- **Purpose**: Real-time Event Streaming
- **Description**: Kafka serves as the central nervous system of Kai, facilitating real-time event streaming. It enables the system to process KYC events as they occur, ensuring timely analysis and response.

### 1.3 Postgres
- **Purpose**: Data Storage and Sink
- **Description**: Postgres is used for persistent storage of processed data and serves as a data sink. It stores historical data, allowing for retrospective analysis and audit trails.

## 2. LLM (Language Model) Layer

The LLM Layer is where the AI magic happens. It's responsible for understanding and processing KYC-related information. It consists of three components:

### 2.1 Prompt Library
- **Purpose**: Verified Prompts for Agentic Behavior
- **Description**: This library contains a collection of carefully crafted and verified prompts. These prompts guide the AI in exhibiting the correct agentic behavior for various KYC tasks.

### 2.2 Policies and Procedures
- **Purpose**: Guide AI Decision Making
- **Description**: This component, potentially implemented as a RAG (Retrieval-Augmented Generation) Library, contains the policies and procedures that guide the AI's decision-making process. It ensures that the AI adheres to regulatory requirements and best practices in KYC.

### 2.3 Guardrails
- **Purpose**: Quality Checks and Safety Measures
- **Description**: Guardrails are implemented to ensure the quality and safety of AI outputs. They prevent the AI from making decisions that could violate regulations or pose risks to the organization.

## 3. Orchestration Layer

The Orchestration Layer manages the workflow of KYC processes within Kai.

### 3.1 Orchestration
- **Purpose**: Workflow Management
- **Description**: This component, which is yet to be fully determined, will be responsible for managing the complex workflows involved in KYC processes. It will coordinate tasks between different components of the system, ensuring that KYC investigations are carried out efficiently and effectively.

## 4. User Interface Layer

The User Interface Layer is where users interact with Kai.

### 4.1 User Interface
- **Technology**: React
- **Purpose**: Frontend Application
- **Description**: The user interface is built using React, providing a modern, responsive frontend for users to interact with Kai. It allows users to initiate KYC investigations, view results, and manage the KYC process.

## Data Flow

1. Shadowtraffic generates synthetic KYC data.
2. The data is streamed through Kafka in real-time.
3. The LLM Layer processes the events, using the Prompt Library, Policies and Procedures, and Guardrails to make informed decisions.
4. The Orchestration Layer manages the workflow of the KYC process.
5. Results are stored in Postgres for future reference and analysis.
6. Users interact with the system through the React-based User Interface.

## Conclusion

This high-level solution design provides a scalable, efficient, and AI-driven approach to KYC investigations. By leveraging cutting-edge technologies and a layered architecture, Kai is well-positioned to handle the complex requirements of modern KYC processes while providing a user-friendly experience.

For more detailed information on each component, please refer to the respective technical documentation.
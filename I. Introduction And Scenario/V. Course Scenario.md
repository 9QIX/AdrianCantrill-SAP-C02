## Course Introduction: Animals for Life – Scenario Overview

## Overview

The course is grounded in a **realistic fictional scenario** to simulate real-world architectures, decision-making, and AWS implementations. The scenario revolves around a nonprofit called **Animals for Life**, which will be referenced throughout the course to provide context for architectures, deployments, and business decisions.

## About the Organization: Animals for Life

**Animals for Life** is an international nonprofit focused on:

- Animal rescue and medical care (domestic animals)
- Habitat monitoring and preservation (wildlife)
- Global animal rights advocacy and policy influence

### Key Activities:

- Animal tracking and habitat monitoring using IoT
- Collection and analysis of large datasets (Big Data)
- Active field operations supported by remote staff
- Campaigns and awareness programs

## Organizational Structure

### Headquarters:

- **Brisbane, Australia**

  - \~100 staff
  - Core departments: IT, marketing, legal, finance, operations
  - Hosts **on-premises infrastructure** using private address range `192.168.10.0/24`

### Field Workers:

- \~100 globally distributed
- Located in Australia and regions of need
- Roles include:

  - Animal care workers
  - Vets and scientists
  - Data scientists, ML specialists, IoT developers

### Global Offices:

- **London, UK** (EU region)
- **New York, USA** (East Coast)
- **Seattle, USA** (West Coast)

## Existing Infrastructure & Challenges

### Current Technical State:

- Aged on-premises data center in Brisbane
- 5 co-location racks at external facilities
- Previous failed cloud pilots:

  - **AWS Sydney**: Used `10.0.0.0/16`
  - **Azure**: Used `172.31.0.0/16`
  - **Google Cloud**: Also trialed, but unsuccessful

- All services consumed centrally from Brisbane by global users

### Problems Identified:

| Category           | Issues                                                              |
| ------------------ | ------------------------------------------------------------------- |
| **Performance**    | Global offices and field workers experience latency and downtime    |
| **Scalability**    | No elasticity, hardware constrained                                 |
| **Availability**   | Maintenance downtime during critical hours for overseas workers     |
| **Cost**           | Expensive global deployments and data center maintenance            |
| **Cloud Adoption** | Poorly executed pilots with no cost or performance improvements     |
| **Staffing**       | Small IT team unfamiliar with automation and cloud-native practices |

## Field Work Requirements

- Field staff use **rugged laptops** with:

  - 3G/4G or Satellite connectivity

- Data needs vary:

  - Emails and small file sharing
  - Chat and collaboration apps
  - Uploading large datasets from sensors and monitoring tools

### Operational Needs:

- 24/7 availability across time zones
- High resilience and speed
- Central system failures directly impact mission-critical work

## Strategic Goals & Future Needs

### High-Level Requirements:

1. **Performance**:

   - Fast, consistent experience for field and global staff
   - Reduce reliance on Brisbane as the central hub

2. **Global Agility**:

   - Ability to deploy infrastructure **on-demand** in new regions
   - Must support **disaster recovery and temporary scaling**

3. **Cost Optimization**:

   - Infrastructure should scale down when not needed
   - Minimal baseline cost, leveraging **pay-as-you-go models**

4. **Scalability**:

   - Rapid, elastic growth during:

     - Seasonal campaigns
     - Disaster response
     - Social media pushes

5. **Emerging Tech Adoption**:

   - Need support for:

     - IoT
     - Machine Learning
     - Big Data & Analytics

6. **Automation First**:

   - Minimize IT overhead
   - Shift focus from support functions to mission-driven activities
   - Use automation to replace manual provisioning

## Lessons for Real-World Application

- Not all scenario details are provided intentionally.
- Part of the learning experience is determining what information is **missing** and what questions to ask.
- This mirrors real-world solution architecture where **assumptions must be validated** and **requirements must be clarified**.

## AWS Exam Relevance

- AWS certification exams (SAP-C02 included) are **scenario-driven**.
- This course prepares you to:

  - **Extract relevant information**
  - **Discard distractors**
  - **Make decisions** based on limited but practical context

- By embedding learning in a realistic organization, you gain hands-on exposure to **real-world AWS architecture principles**.

## Visual Network Summary (Described in Lesson)

- **Brisbane Office**:

  - `192.168.10.0/24` (on-premises)

- **AWS Pilot (Sydney)**:

  - `10.0.0.0/16` (VPC CIDR block)

- **Azure Pilot**:

  - `172.31.0.0/16`

These will become important later when discussing VPC peering, network design, and IP conflicts.

## Summary

The _Animals for Life_ scenario offers a realistic, evolving case study to anchor AWS SAP-C02 topics. Through this approach, the course teaches not only AWS technical details, but also **how to think like a solutions architect**:

- Identifying core business problems
- Evaluating architectural trade-offs
- Applying the right AWS services
- Making scalable, cost-effective, and highly available solutions

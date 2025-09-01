# POC Platform

## Problem

Mr. Bill wants a system to keep track of his favorite POCs. You need to build a mobile app where Mr. Bill can register all his POCs. He also needs to be able to search POCs by name, by language, and by tags. This system should be multi-tenant, because Mr. Bill plans to sell it to a number of people in Brazil. It must also have the ability to generate reports and create a video compilation of all the POCs users have created over the course of a year. The system must be secure, include proper login functionality, and support real-time Dojos using the platform you will build for Mr. Bill.

**Restrictions**

- No use of AWS Lambda.
- No monolithic architectures.
- No single-AZ solutions.
- Mobile framework restriction: Ionic is not allowed.
- Mobile must use a single native language.
- No use of MongoDB.
- Having a single relational database is not allowed.
- No cloud providers other than AWS.

## Requirements

### Features

1. Mobile app.
1. Login.
1. Save POCs.
1. Search POCs by name, language, and tags.
1. Generate reports.
1. Generate a video with all the POCs the user has created over the course of a year.
1. Real-time Dojos.

### Other requirements

1. Multi-tenancy.
1. Brazilian.

## Solution

### Components

1. Mobile app
1. Web app
1. Auth service
1. Billing service
1. POC service
1. Repository integration service
1. Snapshot processor
1. Search service
1. Analytics processor
1. Analytics service
1. Report service
1. Video compilation service
1. Dojo service
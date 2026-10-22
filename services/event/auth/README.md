# TicketBookingSystemV2 Authentication Module

## Overview
Authentication system for customer/organizer/admin roles with OAuth2, JWT, and MFA.

## Files
- `services/event/auth.py` - Authentication logic
- `services/event/models.py` - User and role models
- `services/event/middleware.py` - Auth middleware and RBAC
- `services/event/routes/auth.py` - Auth API routes
- `services/event/mfa.py` - Multi-factor authentication

## Features
- Customer, organizer, and admin account registration/login
- JWT-based session management
- OAuth2 provider integration
- Role-Based Access Control (RBAC)
- Multi-factor authentication (MFA)
- Password hashing with bcrypt
- Session timeout handling

## Tech Stack
- Node.js/Express
- MySQL
- JWT
- OAuth2
- bcrypt for password hashing

## Day 5 Outcome
Authentication module implemented. 1 PR created targeting development.
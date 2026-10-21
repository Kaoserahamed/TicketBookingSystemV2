# Day 2 Architecture: Service Boundaries, Database Schema, API Contracts

## Repository
`TicketBookingSystemV2` — Ticket Booking System (MySQL, Node.js + Express, React + TypeScript)

## Proposed Concept
Comprehensive ticket booking platform with dynamic pricing, seat selection maps, event management, mobile tickets, refund/exchange system, and partner integrations.

## Service Boundaries
| Service | Responsibility | Technology |
|---------|---------------|------------|
| Event Service | Event CRUD, publishing, postponement, cancellation | Node.js/Express, MySQL |
| Venue Service | Venue details, capacity, layout, sections, amenities | Node.js/Express, MySQL |
| Ticket Service | Ticket types, pricing, availability, sales windows | Node.js/Express, MySQL |
| Booking Service | Seat holds, booking creation, payment, confirmation | Node.js/Express, MySQL, Redis |
| Payment Service | Stripe/PayPal, Apple Pay, Google Pay, refunds | Node.js/Express, Stripe API |
| Inventory Service | Ticket availability, overselling prevention, concurrency | Node.js/Express, Redis |
| Dynamic Pricing Service | Demand-based, time-based, surge pricing | Node.js/Express, MySQL |
| Mobile Ticket Service | QR code generation, wallet integration, offline access | Node.js/Express, object storage |
| Refund/Exchange Service | Refund processing, exchanges, price differences | Node.js/Express, MySQL |
| Partner Service | Venue POS, hotel/transport packages, affiliate tracking | Node.js/Express, API integrations |
| Frontend | Event browsing, seat map, booking, mobile tickets | React, TypeScript, Vite |

## Database Schema (MySQL 8+)
- `users` — id, email, password_hash, role (customer/organizer/admin), created_at
- `customers` — id, user_id, booking_history, payment_methods, mobile_tickets, preferences, created_at
- `organizers` — id, user_id, organization_info, verified_status, payout_settings, team_members, created_at
- `events` — id, organizer_id, name, description, category, date_time, venue_id, image_url, status, created_at
- `venues` — id, name, address, capacity, layout, sections, amenities, created_at
- `seat_maps` — id, venue_id, sections, rows, seats, pricing_tiers, accessibility_seats, created_at
- `ticket_types` — id, event_id, type (general_admission/reserved/vip/group), price, quantity, available, sales_start, sales_end, created_at
- `seat_holds` — id, event_id, seat_id, user_id, held_at, expires_at, released
- `bookings` — id, customer_id, event_id, status (pending/confirmed/cancelled/refunded), total, payment_status, created_at, updated_at
- `booking_line_items` — id, booking_id, ticket_type_id, seat_id, price
- `payments` — id, booking_id, amount, method, transaction_id, status, created_at
- `refunds` — id, booking_id, amount, reason, status, processed_at, created_at
- `exchanges` — id, original_booking_id, new_booking_id, price_difference, exchange_fee, status, created_at
- `waitlist` — id, event_id, customer_id, ticket_type_id, notified_at, created_at
- `mobile_tickets` — id, booking_id, qr_code, wallet_url, offline_access, created_at
- `ticket_transfers` — id, ticket_id, from_user_id, to_user_id, status, created_at
- `dynamic_pricing_rules` — id, event_id, type (demand/time/surge), multiplier, conditions, created_at
- `partner_integrations` — id, organizer_id, type (pos/hotel/transport), config, created_at

## API Contracts
### Events
- `GET /api/v1/events` → list (search, filters: date, category, location)
- `POST /api/v1/events` — {name, dateTime, venueId, category} → {event}
- `GET /api/v1/events/{id}` → {event, venue, ticketTypes}
- `PUT /api/v1/events/{id}` → {event}
- `POST /api/v1/events/{id}/publish` → 204
- `POST /api/v1/events/{id}/cancel` → 204

### Venues & Seats
- `GET /api/v1/venues` → list
- `GET /api/v1/venues/{id}/seat-map` → {sections, rows, seats, availability}
- `GET /api/v1/events/{id}/ticket-types` → list

### Booking
- `POST /api/v1/bookings/seat-holds` — {eventId, seatIds} → {holdId, expiresAt}
- `POST /api/v1/bookings` — {holdId, paymentMethod} → {bookingId}
- `GET /api/v1/bookings/{id}` → {booking, lineItems, tickets}
- `GET /api/v1/bookings` → list (customer view)

### Payment
- `POST /api/v1/payments` — {bookingId, method} → {payment}
- `POST /api/v1/payments/{id}/refund` — {amount, reason} → {refund}

### Refunds & Exchanges
- `POST /api/v1/refunds` — {bookingId, amount, reason} → {refund}
- `POST /api/v1/exchanges` — {originalBookingId, newTickets} → {exchange, priceDifference}

### Mobile Tickets
- `GET /api/v1/mobile-tickets/{bookingId}/qr` → {qrCode, walletUrl}
- `POST /api/v1/mobile-tickets/{id}/transfer` — {toUserId} → 204

### Waitlist
- `POST /api/v1/waitlist` — {eventId, ticketTypeId} → {waitlistId}
- `GET /api/v1/waitlist/notifications` → list

### Organizer
- `GET /api/v1/organizer/dashboard` → {sales, revenue, attendees}
- `GET /api/v1/organizer/events` → list
- `GET /api/v1/organizer/attendees/{eventId}` → list

### Check-in
- `POST /api/v1/check-in/scan` — {qrCode} → {attendee, status}

## Technical Notes
- Currently Node.js + Express + MySQL + React; hermetic test suites
- Seat hold mechanism with auto-release and concurrency control
- No dynamic pricing, seat maps, mobile tickets, refunds, or exchanges yet
- No event management tools, membership programs, resale marketplace, or gift cards yet
- No CI/CD pipelines visible; Makefile and docker-compose exist
- No distributed tracing or multi-venue support yet

## Day 2 Outcome
Architecture design documented. No code changes. 0 PRs. Ready for Day 3 (CI/CD pipeline).
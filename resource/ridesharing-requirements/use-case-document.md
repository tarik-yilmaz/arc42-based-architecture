# Use Case Document: RideShare App

## Introduction:

The RideShare app is a mobile application designed to provide users with an affordable and convenient transportation option through ride sharing. The app allows users to connect with nearby drivers and share rides, reducing the number of cars on the road and promoting sustainable transportation. This document outlines the use cases for the RideShare app and how users can interact with the app to book and share rides.

## Actors:

- Passenger: A user who is seeking a ride through the RideShare app.
- Driver: A user who is offering a ride through the RideShare app.
- Payment Processor: A third-party system that handles payment transactions for rides booked through the app.
- Use Cases:

### Register Account

Actors: Passenger, Driver
Description: Allows users to create a new account by providing basic information such as name, email, phone number, and payment method.
Preconditions: User has downloaded and installed the RideShare app.
Postconditions: User is registered and can access the app's features.

### Search for Rides

- Actors: Passenger
- Description: Allows passengers to search for available rides based on location, destination, and desired travel time.
- Preconditions: User has registered and logged into the RideShare app.
- Postconditions: List of available rides is displayed, with details such as driver name, vehicle type, and estimated arrival time.

### Book a Ride

- Actors: Passenger
- Description: Allows passengers to select and book a ride from the list of available rides.
- Preconditions: User has searched for available rides and selected a preferred ride.
- Postconditions: Passenger is notified that the ride has been booked and the driver is notified of the ride request.

### Accept/Decline Ride Request

- Actors: Driver
- Description: Allows drivers to view and accept or decline ride requests from passengers.
- Preconditions: User has registered and logged into the RideShare app as a driver.
- Postconditions: Driver is notified of ride request acceptance or decline.

### Track Ride

- Actors: Passenger, Driver
- Description: Allows passengers and drivers to track the progress of the ride and monitor estimated arrival time.
- Preconditions: Ride has been booked and accepted by driver.
- Postconditions: Real-time updates on ride location and progress are displayed.

### Complete Ride

- Actors: Passenger, Driver, Payment Processor
- Description: Allows passengers and drivers to confirm that the ride has been completed and process payment through the app.
- Preconditions: Ride has reached the destination and the passenger has confirmed the ride completion.
- Postconditions: Payment is processed and both passenger and driver can rate and review each other.

### View Ride History

- Actors: Passenger, Driver
- Description: Allows users to view their ride history, including details such as ride date, time, location, and payment information.
- Preconditions: User has registered and logged into the RideShare app.
- Postconditions: User can view their ride history and access details for individual rides.

## Conclusion:

The RideShare app is designed to provide a user-friendly and reliable ride sharing experience for both passengers and drivers. By enabling users to connect with nearby drivers and share rides, the app promotes sustainable transportation and reduces traffic congestion. This use case document outlines the key interactions and functionalities of the app, helping users to better understand and utilize its features.
#Railway Reservation System(RRS)
import json

# Constants
MAX_SEATS = 50
MAX_NAME_LENGTH = 30
MAX_PHONE_LENGTH = 15
USERS_FILE = "users.json"
BOOKINGS_FILE = "bookings.json"

# Seat class to represent individual seats
class Seat:
    def __init__(self, seatNumber):
        # Initializing seat properties
        self.seatNumber = seatNumber
        self.isBooked = False
        self.passengerName = "Available"
        self.age = 0
        self.phoneNumber = ""
        self.gender = '-'

# Function to initialize a list of Seat objects representing available seats
def initializeSeats():
    return [Seat(i + 1) for i in range(MAX_SEATS)]

# Function to display available seats
def displayAvailableSeats(seats):
    print("Available Seats:")
    for seat in seats:
        if not seat.isBooked:
            print(f"Seat {seat.seatNumber}: {seat.passengerName}")

# Function to display booked seat details
def viewBookingDetails(seats):
    print("Booking Details:")
    for seat in seats:
        if seat.isBooked:
            print(f"Seat {seat.seatNumber} is booked by {seat.passengerName} (Age: {seat.age}, Gender: {seat.gender}, Phone: {seat.phoneNumber})")

# Function to book a ticket for a seat
def bookTicket(seats):
    seatChoice = int(input("Enter the seat number you want to book: "))
    if seatChoice < 1 or seatChoice > MAX_SEATS:
        print(f"Invalid seat number. Please choose a seat between 1 and {MAX_SEATS}.")
        return
    
    seat = seats[seatChoice - 1]
    if seat.isBooked:
        print(f"Seat {seatChoice} is already booked.")
    else:
        seat.isBooked = True
        seat.passengerName = input("Enter passenger name: ")
        seat.age = int(input("Enter age: "))
        seat.gender = input("Enter gender (M/F): ")
        seat.phoneNumber = input("Enter phone number: ")
        print(f"Seat {seatChoice} booked successfully for {seat.passengerName}.")

# Function to cancel a booked seat
def cancelBooking(seats):
    seatChoice = int(input("Enter the seat number you want to cancel booking for: "))
    if seatChoice < 1 or seatChoice > MAX_SEATS:
        print(f"Invalid seat number. Please choose a seat between 1 and {MAX_SEATS}.")
        return
    
    seat = seats[seatChoice - 1]
    if not seat.isBooked:
        print(f"Seat {seatChoice} is not booked.")
    else:
        seat.isBooked = False
        seat.passengerName = "Available"
        seat.age = 0
        seat.phoneNumber = ""
        seat.gender = '-'
        print(f"Booking for seat {seatChoice} canceled successfully.")

# Function to save user and booking data to respective JSON files
def saveData(users, seats):
    with open(USERS_FILE, 'w') as user_file:
        json.dump(users, user_file)
    
    with open(BOOKINGS_FILE, 'w') as booking_file:
        bookings = [{
            'seatNumber': seat.seatNumber,
            'isBooked': seat.isBooked,
            'passengerName': seat.passengerName,
            'age': seat.age,
            'phoneNumber': seat.phoneNumber,
            'gender': seat.gender
        } for seat in seats]
        json.dump(bookings, booking_file)

# Function to load user and booking data from JSON files
def loadData():
    try:
        with open(USERS_FILE, 'r') as user_file:
            users = json.load(user_file)
    except FileNotFoundError:
        users = []
    
    try:
        with open(BOOKINGS_FILE, 'r') as booking_file:
            bookings = json.load(booking_file)
            seats = [Seat(seat_data['seatNumber']) for seat_data in bookings]
            for index, seat_data in enumerate(bookings):
                seat = seats[index]
                seat.isBooked = seat_data['isBooked']
                seat.passengerName = seat_data['passengerName']
                seat.age = seat_data['age']
                seat.phoneNumber = seat_data['phoneNumber']
                seat.gender = seat_data['gender']
    except FileNotFoundError:
        seats = initializeSeats()
    
    return users, seats

# Function for user signup
def signUp(users):
    username = input("Enter username: ")
    password = input("Enter password: ")
    users.append({'username': username, 'password': password})
    print("User signed up successfully.")

# Function for user login
def login(users):
    username = input("Enter username: ")
    password = input("Enter password: ")
    for user in users:
        if user['username'] == username and user['password'] == password:
            print("Login successful!")
            return True
    print("Invalid username or password. Please try again.")
    return False

# Main function to execute the program
def main():
    users, seats = loadData()
    loggedIn = False
    
    # User authentication loop
    while not loggedIn:
        print("\nRailway Reservation System")
        print("1. Sign Up")
        print("2. Log In")
        print("3. Exit")
        choice = int(input("Enter your choice: "))
        
        if choice == 1:
            signUp(users)
        elif choice == 2:
            if login(users):
                loggedIn = True
                print("Welcome!")
        elif choice == 3:
            print("Exiting the program. Thank you!")
            return
    
    # Main menu loop after successful login
    while True:
        print("\nRailway Reservation System")
        print("1. Display Available Seats")
        print("2. Book a Ticket")
        print("3. Cancel Booking")
        print("4. View Booking Details")
        print("5. Exit")
        choice = int(input("Enter your choice: "))
        
        if choice == 1:
            displayAvailableSeats(seats)
        elif choice == 2:
            bookTicket(seats)
            saveData(users, seats)
        elif choice == 3:
            cancelBooking(seats)
            saveData(users, seats)
        elif choice == 4:
            viewBookingDetails(seats)
        elif choice == 5:
            print("Exiting the program. Thank you!")
            break
        else:
            print("Invalid choice. Please enter a valid option.")

if __name__ == "__main__":
    main()



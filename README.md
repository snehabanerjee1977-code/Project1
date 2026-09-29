# Project1

  import java.io.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.time.temporal.ChronoUnit;
import java.util.ArrayList;
import java.util.Scanner;

// ===============================
// ROOM CATEGORY
// ===============================
enum RoomCategory {
    STANDARD,
    DELUXE,
    SUITE
}

// ===============================
// ROOM CLASS
// ===============================
class Room {

    private int roomNumber;
    private RoomCategory category;
    private double pricePerNight;
    private boolean available;

    public Room(int roomNumber, RoomCategory category, double pricePerNight) {
        this.roomNumber = roomNumber;
        this.category = category;
        this.pricePerNight = pricePerNight;
        this.available = true;
    }

    public int getRoomNumber() {
        return roomNumber;
    }

    public RoomCategory getCategory() {
        return category;
    }

    public double getPricePerNight() {
        return pricePerNight;
    }

    public boolean isAvailable() {
        return available;
    }

    public void setAvailable(boolean available) {
        this.available = available;
    }

    public void displayRoom() {
        System.out.println(
            "Room: " + roomNumber +
            " | Category: " + category +
            " | Price: Rs." + pricePerNight +
            " | Status: " + (available ? "Available" : "Booked")
        );
    }
}

// ===============================
// CUSTOMER CLASS
// ===============================
class Customer {

    private String name;
    private String phone;
    private String email;

    public Customer(String name, String phone, String email) {
        this.name = name;
        this.phone = phone;
        this.email = email;
    }

    public String getName() {
        return name;
    }

    public String getPhone() {
        return phone;
    }

    public String getEmail() {
        return email;
    }
}

// ===============================
// PAYMENT CLASS
// ===============================
class Payment {

    private String paymentMethod;
    private double amount;
    private boolean successful;

    public Payment(double amount) {
        this.amount = amount;
        this.successful = false;
    }

    public boolean processPayment(Scanner sc) {

        System.out.println("\n========== PAYMENT ==========");
        System.out.println("Amount to Pay: Rs." + amount);

        System.out.println("1. UPI");
        System.out.println("2. Credit/Debit Card");
        System.out.println("3. Cash");

        System.out.print("Choose payment method: ");

        int choice = sc.nextInt();
        sc.nextLine();

        switch (choice) {

            case 1:
                paymentMethod = "UPI";

                System.out.print("Enter UPI ID: ");
                String upi = sc.nextLine();

                if (!upi.isEmpty()) {
                    successful = true;
                }
                break;

            case 2:
                paymentMethod = "Card";

                System.out.print("Enter Card Number: ");
                String card = sc.nextLine();

                if (!card.isEmpty()) {
                    successful = true;
                }
                break;

            case 3:
                paymentMethod = "Cash";
                successful = true;
                break;

            default:
                System.out.println("Invalid payment method.");
                successful = false;
        }

        if (successful) {
            System.out.println("Payment successful!");
        } else {
            System.out.println("Payment failed!");
        }

        return successful;
    }

    public String getPaymentMethod() {
        return paymentMethod;
    }
}

// ===============================
// RESERVATION CLASS
// ===============================
class Reservation {

    private String bookingId;
    private Customer customer;
    private Room room;
    private LocalDate checkIn;
    private LocalDate checkOut;
    private int guests;
    private double totalAmount;
    private String paymentMethod;
    private String status;

    public Reservation(
            String bookingId,
            Customer customer,
            Room room,
            LocalDate checkIn,
            LocalDate checkOut,
            int guests,
            double totalAmount,
            String paymentMethod) {

        this.bookingId = bookingId;
        this.customer = customer;
        this.room = room;
        this.checkIn = checkIn;
        this.checkOut = checkOut;
        this.guests = guests;
        this.totalAmount = totalAmount;
        this.paymentMethod = paymentMethod;
        this.status = "CONFIRMED";
    }

    public String getBookingId() {
        return bookingId;
    }

    public Room getRoom() {
        return room;
    }

    public String getStatus() {
        return status;
    }

    public void cancel() {
        status = "CANCELLED";
    }

    public void displayReservation() {

        System.out.println("\n========================================");
        System.out.println("          BOOKING DETAILS");
        System.out.println("========================================");

        System.out.println("Booking ID      : " + bookingId);
        System.out.println("Customer Name   : " + customer.getName());
        System.out.println("Phone           : " + customer.getPhone());
        System.out.println("Email           : " + customer.getEmail());

        System.out.println("----------------------------------------");

        System.out.println("Room Number     : " + room.getRoomNumber());
        System.out.println("Room Category   : " + room.getCategory());
        System.out.println("Check-in        : " + checkIn);
        System.out.println("Check-out       : " + checkOut);
        System.out.println("Guests          : " + guests);

        long nights = ChronoUnit.DAYS.between(checkIn, checkOut);

        System.out.println("Number of Nights: " + nights);
        System.out.println("Total Amount    : Rs." + totalAmount);
        System.out.println("Payment Method  : " + paymentMethod);
        System.out.println("Status          : " + status);

        System.out.println("========================================");
    }

    public String toFileString() {

        return bookingId + "," +
                customer.getName() + "," +
                customer.getPhone() + "," +
                customer.getEmail() + "," +
                room.getRoomNumber() + "," +
                room.getCategory() + "," +
                checkIn + "," +
                checkOut + "," +
                guests + "," +
                totalAmount + "," +
                paymentMethod + "," +
                status;
    }
}

// ===============================
// HOTEL CLASS
// ===============================
class Hotel {

    private ArrayList<Room> rooms;
    private ArrayList<Reservation> reservations;

    private final String FILE_NAME = "reservations.txt";

    private int bookingCounter = 1001;

    private Scanner sc;

    public Hotel(Scanner sc) {

        this.sc = sc;

        rooms = new ArrayList<>();
        reservations = new ArrayList<>();

        initializeRooms();
        loadReservations();
    }

    // ===============================
    // INITIALIZE ROOMS
    // ===============================
    private void initializeRooms() {

        rooms.add(new Room(101, RoomCategory.STANDARD, 2000));
        rooms.add(new Room(102, RoomCategory.STANDARD, 2000));
        rooms.add(new Room(103, RoomCategory.STANDARD, 2000));

        rooms.add(new Room(201, RoomCategory.DELUXE, 3500));
        rooms.add(new Room(202, RoomCategory.DELUXE, 3500));
        rooms.add(new Room(203, RoomCategory.DELUXE, 3500));

        rooms.add(new Room(301, RoomCategory.SUITE, 6000));
        rooms.add(new Room(302, RoomCategory.SUITE, 6000));
    }

    // ===============================
    // SEARCH ROOMS
    // ===============================
    public void searchRooms() {

        System.out.println("\n========== SEARCH ROOMS ==========");

        System.out.println("1. Standard");
        System.out.println("2. Deluxe");
        System.out.println("3. Suite");
        System.out.println("4. All");

        System.out.print("Choose category: ");

        int choice = sc.nextInt();
        sc.nextLine();

        RoomCategory category = null;

        if (choice == 1) {
            category = RoomCategory.STANDARD;
        }
        else if (choice == 2) {
            category = RoomCategory.DELUXE;
        }
        else if (choice == 3) {
            category = RoomCategory.SUITE;
        }
        else if (choice != 4) {
            System.out.println("Invalid choice.");
            return;
        }

        System.out.println("\nAvailable Rooms:");

        boolean found = false;

        for (Room room : rooms) {

            if (room.isAvailable()) {

                if (choice == 4 ||
                    room.getCategory() == category) {

                    room.displayRoom();
                    found = true;
                }
            }
        }

        if (!found) {
            System.out.println("No rooms available.");
        }
    }

    // ===============================
    // SHOW ALL ROOMS
    // ===============================
    public void showAllRooms() {

        System.out.println("\n========== ALL ROOMS ==========");

        for (Room room : rooms) {
            room.displayRoom();
        }
    }

    // ===============================
    // MAKE RESERVATION
    // ===============================
    public void makeReservation() {

        System.out.println("\n========== MAKE RESERVATION ==========");

        System.out.print("Enter customer name: ");
        String name = sc.nextLine();

        System.out.print("Enter phone number: ");
        String phone = sc.nextLine();

        System.out.print("Enter email: ");
        String email = sc.nextLine();

        Customer customer =
            new Customer(name, phone, email);

        System.out.println("\nSelect Room Category:");

        System.out.println("1. Standard - Rs.2000/night");
        System.out.println("2. Deluxe   - Rs.3500/night");
        System.out.println("3. Suite    - Rs.6000/night");

        System.out.print("Enter choice: ");

        int categoryChoice = sc.nextInt();
        sc.nextLine();

        RoomCategory category;

        if (categoryChoice == 1) {
            category = RoomCategory.STANDARD;
        }
        else if (categoryChoice == 2) {
            category = RoomCategory.DELUXE;
        }
        else if (categoryChoice == 3) {
            category = RoomCategory.SUITE;
        }
        else {
            System.out.println("Invalid category.");
            return;
        }

        System.out.println("\nAvailable rooms:");

        boolean found = false;

        for (Room room : rooms) {

            if (room.getCategory() == category &&
                room.isAvailable()) {

                room.displayRoom();
                found = true;
            }
        }

        if (!found) {
            System.out.println(
                "No rooms available in this category."
            );
            return;
        }

        System.out.print("\nEnter room number: ");

        int roomNumber = sc.nextInt();
        sc.nextLine();

        Room selectedRoom = findRoom(roomNumber);

        if (selectedRoom == null ||
            !selectedRoom.isAvailable() ||
            selectedRoom.getCategory() != category) {

            System.out.println(
                "Invalid or unavailable room."
            );
            return;
        }

        DateTimeFormatter formatter =
            DateTimeFormatter.ofPattern("dd-MM-yyyy");

        System.out.print(
            "Enter check-in date (dd-MM-yyyy): "
        );

        String checkInString = sc.nextLine();

        System.out.print(
            "Enter check-out date (dd-MM-yyyy): "
        );

        String checkOutString = sc.nextLine();

        LocalDate checkIn;
        LocalDate checkOut;

        try {

            checkIn =
                LocalDate.parse(checkInString, formatter);

            checkOut =
                LocalDate.parse(checkOutString, formatter);

        }
        catch (Exception e) {

            System.out.println(
                "Invalid date format."
            );
            return;
        }

        if (!checkOut.isAfter(checkIn)) {

            System.out.println(
                "Check-out date must be after check-in date."
            );

            return;
        }

        System.out.print("Enter number of guests: ");

        int guests = sc.nextInt();
        sc.nextLine();

        if (guests <= 0) {

            System.out.println(
                "Invalid number of guests."
            );

            return;
        }

        long nights =
            ChronoUnit.DAYS.between(
                checkIn,
                checkOut
            );

        double totalAmount =
            nights * selectedRoom.getPricePerNight();

        System.out.println("\n========== BILL ==========");

        System.out.println(
            "Room: " + selectedRoom.getRoomNumber()
        );

        System.out.println(
            "Category: " + selectedRoom.getCategory()
        );

        System.out.println(
            "Nights: " + nights
        );

        System.out.println(
            "Price/night: Rs." +
            selectedRoom.getPricePerNight()
        );

        System.out.println(
            "Total Amount: Rs." + totalAmount
        );

        Payment payment =
            new Payment(totalAmount);

        boolean paymentSuccessful =
            payment.processPayment(sc);

        if (!paymentSuccessful) {

            System.out.println(
                "Booking cancelled because payment failed."
            );

            return;
        }

        String bookingId =
            "BK" + bookingCounter++;

        Reservation reservation =
            new Reservation(
                bookingId,
                customer,
                selectedRoom,
                checkIn,
                checkOut,
                guests,
                totalAmount,
                payment.getPaymentMethod()
            );

        reservations.add(reservation);

        selectedRoom.setAvailable(false);

        saveReservation(reservation);

        System.out.println(
            "\nBOOKING SUCCESSFUL!"
        );

        reservation.displayReservation();
    }

    // ===============================
    // FIND ROOM
    // ===============================
    private Room findRoom(int roomNumber) {

        for (Room room : rooms) {

            if (room.getRoomNumber() == roomNumber) {
                return room;
            }
        }

        return null;
    }

    // ===============================
    // VIEW BOOKING
    // ===============================
    public void viewBooking() {

        System.out.println(
            "\n========== VIEW BOOKING =========="
        );

        System.out.print("Enter Booking ID: ");

        String bookingId = sc.nextLine();

        for (Reservation reservation : reservations) {

            if (reservation.getBookingId()
                    .equalsIgnoreCase(bookingId)) {

                reservation.displayReservation();
                return;
            }
        }

        System.out.println(
            "Booking not found."
        );
    }

    // ===============================
    // CANCEL BOOKING
    // ===============================
    public void cancelBooking() {

        System.out.println(
            "\n========== CANCEL BOOKING =========="
        );

        System.out.print("Enter Booking ID: ");

        String bookingId = sc.nextLine();

        for (Reservation reservation : reservations) {

            if (reservation.getBookingId()
                    .equalsIgnoreCase(bookingId)) {

                if (reservation.getStatus()
                        .equals("CANCELLED")) {

                    System.out.println(
                        "This booking is already cancelled."
                    );

                    return;
                }

                reservation.cancel();

                reservation.getRoom()
                           .setAvailable(true);

                updateFile();

                System.out.println(
                    "Booking cancelled successfully."
                );

                return;
            }
        }

        System.out.println(
            "Booking not found."
        );
    }

    // ===============================
    // SAVE RESERVATION
    // ===============================
    private void saveReservation(
            Reservation reservation) {

        try {

            FileWriter writer =
                new FileWriter(FILE_NAME, true);

            writer.write(
                reservation.toFileString() + "\n"
            );

            writer.close();

        }
        catch (IOException e) {

            System.out.println(
                "Error saving reservation."
            );
        }
    }

    // ===============================
    // LOAD RESERVATIONS
    // ===============================
    private void loadReservations() {

        File file =
            new File(FILE_NAME);

        if (!file.exists()) {
            return;
        }

        try {

            BufferedReader reader =
                new BufferedReader(
                    new FileReader(FILE_NAME)
                );

            String line;

            while ((line = reader.readLine()) != null) {

                String[] data =
                    line.split(",");

                if (data.length < 12) {
                    continue;
                }

                String bookingId = data[0];

                String name = data[1];
                String phone = data[2];
                String email = data[3];

                int roomNumber =
                    Integer.parseInt(data[4]);

                LocalDate checkIn =
                    LocalDate.parse(data[6]);

                LocalDate checkOut =
                    LocalDate.parse(data[7]);

                int guests =
                    Integer.parseInt(data[8]);

                double amount =
                    Double.parseDouble(data[9]);

                String paymentMethod =
                    data[10];

                String status =
                    data[11];

                Room room =
                    findRoom(roomNumber);

                if (room == null) {
                    continue;
                }

                Customer customer =
                    new Customer(
                        name,
                        phone,
                        email
                    );

                Reservation reservation =
                    new Reservation(
                        bookingId,
                        customer,
                        room,
                        checkIn,
                        checkOut,
                        guests,
                        amount,
                        paymentMethod
                    );

                if (status.equals("CANCELLED")) {

                    reservation.cancel();

                }
                else {

                    room.setAvailable(false);
                }

                reservations.add(reservation);

                try {

                    int number =
                        Integer.parseInt(
                            bookingId.substring(2)
                        );

                    if (number >= bookingCounter) {
                        bookingCounter = number + 1;
                    }

                }
                catch (Exception ignored) {
                }
            }

            reader.close();

        }
        catch (IOException e) {

            System.out.println(
                "Error loading reservations."
            );
        }
    }

    // ===============================
    // UPDATE FILE
    // ===============================
    private void updateFile() {

        try {

            FileWriter writer =
                new FileWriter(FILE_NAME);

            for (Reservation reservation :
                    reservations) {

                writer.write(
                    reservation.toFileString()
                    + "\n"
                );
            }

            writer.close();

        }
        catch (IOException e) {

            System.out.println(
                "Error updating reservation file."
            );
        }
    }
}

// ===============================
// MAIN CLASS
// ===============================
public class Main {

    public static void main(String[] args) {

        Scanner sc =
            new Scanner(System.in);

        Hotel hotel =
            new Hotel(sc);

        int choice;

        do {

            System.out.println("\n");
            System.out.println(
                "========================================"
            );

            System.out.println(
                "       HOTEL RESERVATION SYSTEM"
            );

            System.out.println(
                "========================================"
            );

            System.out.println(
                "1. Search Available Rooms"
            );

            System.out.println(
                "2. Make Reservation"
            );

            System.out.println(
                "3. View Booking Details"
            );

            System.out.println(
                "4. Cancel Reservation"
            );

            System.out.println(
                "5. View All Rooms"
            );

            System.out.println(
                "6. Exit"
            );

            System.out.println(
                "========================================"
            );

            System.out.print(
                "Enter your choice: "
            );

            choice = sc.nextInt();
            sc.nextLine();

            switch (choice) {

                case 1:
                    hotel.searchRooms();
                    break;

                case 2:
                    hotel.makeReservation();
                    break;

                case 3:
                    hotel.viewBooking();
                    break;

                case 4:
                    hotel.cancelBooking();
                    break;

                case 5:
                    hotel.showAllRooms();
                    break;

                case 6:
                    System.out.println(
                        "Thank you for using Hotel Reservation System!"
                    );
                    break;

                default:
                    System.out.println(
                        "Invalid choice. Try again."
                    );
            }

        }
        while (choice != 6);

        sc.close();
    }
}



#include <iostream>
#include <vector>
#include <string>
#include <map>
#include <ctime>
#include <algorithm>
#include <iomanip>

using namespace std;

// Abstract Base Class
class Item {
protected:
    int itemID;
    string title;
    bool availability;

public:
    Item(int id, string t) : itemID(id), title(t), availability(true) {}
    virtual ~Item() {}

    virtual void getItemDetails() const = 0;
    virtual bool checkAvailability() const = 0;
    virtual void checkOut() = 0;
    virtual void checkIn() = 0;
};

// Derived Class for Book
class Book : public Item {
private:
    string author;
    string ISBN;

public:
    Book(int id, string t, string a, string isbn) : Item(id, t), author(a), ISBN(isbn) {}

    void getItemDetails() const override {
        cout << "Book ID: " << itemID << ", Title: " << title << ", Author: " << author << ", ISBN: " << ISBN << endl;
    }

    bool checkAvailability() const override {
        return availability;
    }

    void checkOut() override {
        if (availability) {
            availability = false;
            cout << title << " has been checked out." << endl;
        } else {
            cout << title << " is currently unavailable." << endl;
        }
    }

    void checkIn() override {
        availability = true;
        cout << title << " has been checked in." << endl;
    }
};

// Derived Class for Journal
class Journal : public Item {
private:
    string publisher;
    int issueNumber;

public:
    Journal(int id, string t, string p, int issue) : Item(id, t), publisher(p), issueNumber(issue) {}

    void getItemDetails() const override {
        cout << "Journal ID: " << itemID << ", Title: " << title << ", Publisher: " << publisher << ", Issue Number: " << issueNumber << endl;
    }

    bool checkAvailability() const override {
        return availability;
    }

    void checkOut() override {
        if (availability) {
            availability = false;
            cout << title << " has been checked out." << endl;
        } else {
            cout << title << " is currently unavailable." << endl;
        }
    }

    void checkIn() override {
        availability = true;
        cout << title << " has been checked in." << endl;
    }
};

// Class for Member
class Member {
private:
    int memberID;
    string name;
    string contactInfo;
    int loanLimit;
    vector<int> loanHistory;

public:
    Member(int id, string n, string contact, int limit) : memberID(id), name(n), contactInfo(contact), loanLimit(limit) {}

    void borrowItem(Item& item) {
        if (loanHistory.size() < loanLimit) {
            item.checkOut();
            loanHistory.push_back(itemID);
        } else {
            cout << "Loan limit reached for member: " << name << endl;
        }
    }

    void returnItem(Item& item) {
        item.checkIn();
        loanHistory.erase(remove(loanHistory.begin(), loanHistory.end(), item.itemID), loanHistory.end());
    }

    void getLoanHistory() const {
        cout << "Loan History for " << name << ": ";
        for (int id : loanHistory) {
            cout << id << " ";
        }
        cout << endl;
    }
};

// Class for Loan
class Loan {
private:
    int loanID;
    Item* item;
    Member* member;
    time_t dueDate;
    time_t actualReturnDate;

public:
    Loan(int id, Item* it, Member* mem, time_t due) : loanID(id), item(it), member(mem), dueDate(due), actualReturnDate(0) {}

    double calculateFine() {
        if (actualReturnDate > dueDate) {
            double finePerDay = 0.5; // Fine of 0.5 per day
            double overdueDays = difftime(actualReturnDate, dueDate) / (60 * 60 * 24);
            return finePerDay * overdueDays;
        }
        return 0.0;
    }

    void returnItem() {
        actualReturnDate = time(0); // Set to current time
        item->checkIn();
        cout << "Fine for loan ID " << loanID << ": $" << fixed << setprecision(2) << calculateFine() << endl;
    }
};

// Reservation System
class ReservationSystem {
private:
    map<int, vector<int>> reservations; // itemID -> list of memberIDs

public:
    void reserveItem(int itemID, int memberID) {
        reservations[itemID].push_back(memberID);
        cout << "Item ID " << itemID << " reserved for Member ID " << memberID << endl;
    }

    void showReservations() {
        for (const auto& entry : reservations) {
            cout << "Item ID " << entry.first << " reserved by Members: ";
            for (int memberID : entry.second) {
                cout << memberID << " ";
            }
            cout << endl;
        }
    }
};

// Main Function
int main() {
    // Create items
    Book book1(1, "C++ Programming", "Bjarne Stroustrup", "978-0134997834");
    Journal journal1(2, "Nature", "Nature Publishing Group", 123);

    // Create members
    Member member1(1, "Alice", "alice@example.com", 5);
    Member member2(2, "Bob", "bob@example.com", 3);

    // Create reservation system
    ReservationSystem reservationSystem;

    // Demonstrate functionality
    book1.getItemDetails();
    journal1.getItemDetails();

    member1.borrowItem(book1);
    member1.getLoanHistory();

    member1.returnItem(book1);
    member1.getLoanHistory();

    // Reserve an item
    reservationSystem.reserveItem(2, 1);
    reservationSystem.showReservations();

    return 0;
}

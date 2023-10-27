# Bank-Account-Management
Bank Account Management System
code:
#include <iostream>
#include <string>
#include <cctype>

using namespace std;

class Bank {
public:
    string name;
    long ID;
    string address;
    string contact;

public:
    Bank() {}

    Bank(const string& n, long id, const string& addr, const string& con)
        : address(addr), contact(con) {
        if (validateName(n)) {
            name = n;
        } else {
            cout << "Invalid name format. Name must be a valid string." << endl;
            name = "Invalid Name";
        }
        ID = id;
        if (!validateContact(contact)) {
            cout << "Invalid contact number. Contact must be a 10-digit number." << endl;
            contact = "Invalid Contact";
        }
    }

    bool validateName(const string& str) {
        for (char c : str) {
            if (!isalpha(c) && !isspace(c)) {      //from cctype library
                return false;
            }
        }
        return true;
    }

    bool validateContact(const string& contact) {
        if (contact.length() != 10) {
            return false;
        }
        for (char c : contact) {
            if (!isdigit(c)) {
                return false;
            }
        }
        return true;
    }

    void display() {
        cout << "Name: " << name << endl;
        cout << "ID: " << ID << endl;
        cout << "Address: " << address << endl;
        cout << "Contact: " << contact << endl;
    }
};

class BankAccount : public Bank {                                       //inherited
private:
    long cash;
    string password;

public:
    BankAccount() : cash(0) {}

    BankAccount(const string& n, long id, const string& addr, const string& con, long money, const string& pass)
        : Bank(n, id, addr, con), cash(money), password(pass) {}

    void display() {
        Bank::display();                                            //method overriding
        cout << "Cash: " << cash << endl;
    }

    void deposit(long amount) {
        cash += amount;
        cout << "Deposited " << amount << " into the account. New balance: " << cash << endl;
    }

    bool validatePassword(const string& inputPassword) {
        return password == inputPassword;
    }

    void changePassword(const string& oldPassword, const string& newPassword) {
        if (validatePassword(oldPassword)) {
            password = newPassword;
            cout << "Password changed successfully." << endl;
        } else {
            cout << "Incorrect previous password. Password not changed." << endl;
        }
    }

    void withdraw(long amount) {
        cout << "Enter your password for verification: ";
        string inputPassword;
        cin >> inputPassword;

        if (validatePassword(inputPassword)) {
            if (amount <= cash) {
                cash -= amount;
                cout << "Withdrawn " << amount << " from the account. New balance: " << cash << endl;
            } else {
                cout << "Insufficient funds for withdrawal." << endl;
            }
        } else {
            cout << "Incorrect password. Withdrawal not allowed." << endl;
        }
    }
};

int main() {
    BankAccount accounts[100];
    int total = 0;

    while (true) {
        cout << "Choose an option:" << endl;
        cout << "1. Create a new account" << endl;
        cout << "2. View customer details" << endl;
        cout << "3. Deposit to account" << endl;
        cout << "4. Withdraw from account" << endl;
        cout << "5. Change password" << endl;
        cout << "6. Exit" << endl;

        int choice;
        cin >> choice;

        switch (choice) {
            case 1: {
                // Create a new account
                string name, address, password, contact;
                long ID, cash;

                cout << "Enter name: ";
                cin.ignore();
                getline(cin, name);

                bool containsDigits = false;
                for (char c : name) {
                    if (isdigit(c)) {
                        containsDigits = true;
                        break;
                    }
                }

                if (containsDigits) {
                    cout << "Invalid input for name. Name must be a valid string without digits." << endl;
                    break;
                }

                cout << "Enter ID: ";
                cin >> ID;
                cout << "Enter address: ";
                cin.ignore();
                getline(cin, address);
                cout << "Enter contact (10 digits): ";
                cin >> contact;

                if (!accounts[0].validateContact(contact)) {
                    cout << "Invalid contact number. Contact must be a 10-digit number." << endl;
                    break;
                }

                cout << "Enter password: ";
                cin >> password;
                cout << "Enter initial cash balance: ";
                cin >> cash;

                BankAccount account(name, ID, address, contact, cash, password);
                accounts[total] = account;
                total++;
                cout << "Account created successfully." << endl;
                break;
            }
            case 2: {
                // View customer details
                long searchID;
                cout << "Enter the ID of the customer: ";
                cin >> searchID;

                bool found = false;
                for (int i = 0; i < total; i++) {
                    if (accounts[i].ID == searchID) {
                        accounts[i].display();
                        found = true;
                        break;
                    }
                }

                if (!found) {
                    cout << "Customer not found." << endl;
                }
                break;
            }
            case 3: {
                // Deposit to account
                long depositID;
                long amount;
                cout << "Enter the ID of the customer to deposit to: ";
                cin >> depositID;

                bool found = false;
                for (int i = 0; i < total; i++) {
                    if (accounts[i].ID == depositID) {
                        cout << "Enter the amount to deposit: ";
                        cin >> amount;
                        accounts[i].deposit(amount);
                        found = true;
                        break;
                    }
                }

                if (!found) {
                    cout << "Customer not found." << endl;
                }
                break;
            }
            case 4: {
                // Withdraw from account
                long withdrawID;
                long amount;
                cout << "Enter the ID of the customer to withdraw from: ";
                cin >> withdrawID;

                bool found = false;
                for (int i = 0; i < total; i++) {
                    if (accounts[i].ID == withdrawID) {
                        cout << "Enter the amount to withdraw: ";
                        cin >> amount;
                        accounts[i].withdraw(amount);
                        found = true;
                        break;
                    }
                }

                if (!found) {
                    cout << "Customer not found." << endl;
                }
                break;
            }
            case 5: {
                // Change password
                long changePasswordID;
                cout << "Enter the ID of the customer to change the password: ";
                cin >> changePasswordID;

                bool found = false;
                for (int i = 0; i < total; i++) {
                    if (accounts[i].ID == changePasswordID) {
                        string oldPassword, newPassword;
                        cout << "Enter the old password: ";
                        cin >> oldPassword;

                        if (accounts[i].validatePassword(oldPassword)) {
                            cout << "Enter the new password: ";
                            cin >> newPassword;
                            accounts[i].changePassword(oldPassword, newPassword);
                            found = true;
                        } else {
                            cout << "Incorrect previous password. Password not changed." << endl;
                            found = true;
                        }

                        break;
                    }
                }

                if (!found) {
                    cout << "Customer not found." << endl;
                }
                break;
            }
            case 6:
                cout << "Exiting the program." << endl;
                return 0;
            default:
                cout << "Invalid choice. Please try again." << endl;
        }
    }

    return 0;
}

import json
import random
import datetime
from tabulate import tabulate


class BankingSystem:
    def __init__(self, bank_name):
        self.bank_name = bank_name
        self.users = self.load_data()

    def load_data(self):
        try:
            with open("bank_data.json", "r") as file:
                return json.load(file)
        except FileNotFoundError:
            return {}  # Return an empty dictionary if no data exists

    def save_data(self):
        with open("bank_data.json", "w") as file:
            json.dump(self.users, file, indent=4)

    def generate_account_number(self):
        return random.randint(10000000000, 99999999999)

    def register_username(self):
        print("========= Register Username ==========")
        username = input("Enter the username: ")
        if username in self.users:
            print("This username is already taken! Please try another one.")
            return False

        try:
            password = int(input("Create a new Password (numeric): "))
            transaction_pin = int(input("Create a 4-digit Transaction PIN: "))
        except ValueError:
            print("Invalid format! Please enter numeric values.")
            return False

        email = input("Enter the email ID: ")
        phone_number = input("Enter the phone number: ")
        dob = input("Enter the date of birth (YYYY-MM-DD): ")
        balance = float(input("Enter the initial deposit amount (€): "))

        account_number = self.generate_account_number()

        self.users[username] = {
            "password": password,
            "transaction_pin": transaction_pin,
            "email": email,
            "dob": dob,
            "phone": phone_number,
            "balance": balance,
            "account_number": account_number,
            "transactions": [
                ("Initial Deposit", balance, balance, datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S'))]
        }

        self.save_data()
        print(f"Your account has been successfully created at {self.bank_name}!")
        print(f"Account Number: {account_number}")

    def login(self):
        print("===== Login =====")
        username = input("Enter the username: ")

        if username in self.users:
            attempts = 0
            while attempts < 3:
                try:
                    password = int(input("Enter the password: "))
                except ValueError:
                    print("Invalid password format! Please enter numeric values.")
                    return None

                if self.users[username]["password"] == password:
                    print("Login Successfully")
                    return username
                else:
                    attempts += 1
                    print(f"Invalid credentials, {3 - attempts} attempts left.")

            print("Too many failed attempts.")
            return None
        else:
            print("Username not found.")
            return None

    def show_account_details(self, username):
        print("======= Account Details =======")
        user = self.users[username]
        data = [
            ["Account Holder", username],
            ["A/c Number", user["account_number"]],
            ["Balance", f"€{user['balance']}"]
        ]
        print(tabulate(data, headers=["Field", "Value"], tablefmt="grid"))

    def deposit(self, username):
        amount = float(input("Enter amount to deposit (€): "))
        if amount > 0:
            self.users[username]["balance"] += amount
            self.users[username]["transactions"].append(("Deposit", amount, self.users[username]["balance"],
                                                         datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')))
            self.save_data()
            print(f"€{amount} deposited successfully!")
        else:
            print("Invalid deposit amount.")

    def withdraw(self, username):
        amount = float(input("Enter amount to withdraw (€): "))
        if 0 < amount <= self.users[username]["balance"]:
            self.users[username]["balance"] -= amount
            self.users[username]["transactions"].append(("Withdraw", amount, self.users[username]["balance"],
                                                         datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')))
            self.save_data()
            print(f"€{amount} withdrawn successfully!")
        else:
            print("Invalid withdrawal amount or insufficient funds.")

    def show_transaction_history(self, username):
        if username not in self.users:
            print("User not found!")
            return

        try:
            pin = int(input("Enter your 4-digit Transaction PIN: "))
        except ValueError:
            print("Invalid PIN format!")
            return

        if pin == self.users[username]["transaction_pin"]:
            print("\n=== Transaction History ===")
            transactions = self.users[username]["transactions"]
            transaction_table = [["Type", "Amount", "Balance After", "Date & Time"]]
            transaction_table.extend(transactions)
            print(tabulate(transaction_table, headers="firstrow", tablefmt="grid"))
        else:
            print("Invalid Transaction PIN! Access Denied.")


# Running the Banking System
bank = BankingSystem("My Custom Bank")

while True:
    print("\nWelcome to", bank.bank_name)
    print("1. Register New Account")
    print("2. Login")
    print("3. Exit")

    try:
        choice = int(input("Enter your choice: "))
        if choice == 1:
            bank.register_username()
        elif choice == 2:
            logged_in_user = bank.login()
            if logged_in_user:
                while True:
                    print("\n1. Show Account Details")
                    print("2. Deposit")
                    print("3. Withdraw")
                    print("4. Transaction History")
                    print("5. Logout")

                    try:
                            option = int(input("Enter your choice: "))
                            if option == 1:
                                bank.show_account_details(logged_in_user)
                            elif option == 2:
                                bank.deposit(logged_in_user)
                            elif option == 3:
                                bank.withdraw(logged_in_user)
                            elif option == 4:
                                bank.show_transaction_history(logged_in_user)
                            elif option == 5:
                                print("Logged out successfully!")
                                break
                            else:
                                print("Invalid choice!")
                    except ValueError:
                            print("Enter a valid number!")
            elif choice == 3:
                print("Thank you for using", bank.bank_name)
                break
            else:
                print("Invalid choice!")
    except ValueError:
            print("Enter a valid number!")
            





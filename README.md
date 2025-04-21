import random
from tabulate import tabulate


class BankingSystem:
    def __init__(self, bank_name):
        self.bank_name = bank_name
        self.users = {}

    def show_header(self):
        print(f"======== {self.bank_name} ========")

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
        except ValueError:
            print("Invalid password format! Please enter only numeric values.")
            return False

        email = input("Enter the email ID: ")
        phone_number = input("Enter the phone number: ")
        dob = input("Enter the date of birth (YYYY-MM-DD): ")
        balance = float(input("Enter the initial deposit amount (€): "))
        account_number = self.generate_account_number()

        self.users[username] = {
            "password": password,
            "email": email,
            "dob": dob,
            "phone": phone_number,
            "balance": balance,
            "account_number": account_number,
            "account_holder": username,  # Explicitly storing account holder name
            "transactions": [("Initial Deposit", balance, balance)]
        }

        print(f"Your account has been successfully created! Account Number: {account_number}")
        return True

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

            print("Too many failed attempts. Please authenticate using DOB and Email.")

            dob = input("Enter your Date of Birth (YYYY-MM-DD): ")
            email = input("Enter your Email ID: ")
            if self.users[username]["dob"] == dob and self.users[username]["email"] == email:
                print("Login Successfully using DOB and Email")
                return username
            else:
                print("Invalid credentials, try again.")
                return None
        else:
            print("Username not found, please check again.")
            return None

    def show_account_details(self, username):
        print("======= Account Details =======")
        data = [
            ["Account Holder", self.users[username]['account_holder']],
            ["A/c Number", self.users[username]['account_number']],
            ["Balance", f"€{self.users[username]['balance']}"]
        ]
        print(tabulate(data, headers=["Field", "Value"], tablefmt="grid"))

        edit_option = input("Do you want to edit the account holder's name? (yes/no): ").lower()
        if edit_option == "yes":
            phone_number = input("Enter your registered phone number: ")
            if phone_number == self.users[username]['phone']:
                otp = random.randint(100000, 999999)
                print(f"Your OTP is: {otp}")  # In a real system, send via SMS/email
                try:
                    user_otp = int(input("Enter the OTP: "))
                except ValueError:
                    print("Invalid OTP format.")
                    return

                if user_otp == otp:
                    new_name = input("Enter the new account holder name: ")
                    self.users[username]['account_holder'] = new_name
                    print("Account holder name updated successfully.")
                else:
                    print("Invalid OTP. Edit process aborted.")
            else:
                print("Phone number does not match our records.")
        else:
            print("No changes made.")

    def deposit(self, username):
        amount = float(input("Enter amount to deposit (€): "))
        if amount > 0:
            self.users[username]["balance"] += amount
            self.users[username]["transactions"].append(("Deposit", amount, self.users[username]["balance"]))
            print(f"€{amount} deposited successfully!")
            print(f"New Balance: ₹{self.users[username]['balance']}")
        else:
            print("Invalid deposit amount.")

    def withdraw(self, username):
        amount = float(input("Enter amount to withdraw (€): "))
        if 0 < amount <= self.users[username]["balance"]:
            self.users[username]["balance"] -= amount
            self.users[username]["transactions"].append(("Withdraw", amount, self.users[username]["balance"]))
            print(f"€{amount} withdrawn successfully!")
            print(f"New Balance: €{self.users[username]['balance']}")
        else:
            print("Invalid withdrawal amount or insufficient funds.")

    def show_transaction_history(self, username):
        print("\n=== Transaction History ===")
        transactions = self.users[username]["transactions"]
        transaction_table = [["Type", "Amount", "Balance After"]]
        transaction_table.extend(transactions)
        print(tabulate(transaction_table, headers="firstrow", tablefmt="grid"))


# Running the Banking System
bank = BankingSystem("Federal Bank")

while True:
    bank.show_header()
    choice = input("Do you want to create an account? (yes/no): ").lower()
    if choice == "yes":
        bank.register_username()
    else:
        break

logged_in_users = None
while not logged_in_users:
    logged_in_users = bank.login()

while True:
    bank.show_header()
    print("1. Show Account Details")
    print("2. Deposit")
    print("3. Withdraw")
    print("4. Transaction History")
    print("5. Exit")

    try:
        choice = int(input("Enter your choice: "))
        if choice == 1:
            bank.show_account_details(logged_in_users)
        elif choice == 2:
            bank.deposit(logged_in_users)
        elif choice == 3:
            bank.withdraw(logged_in_users)
        elif choice == 4:
            bank.show_transaction_history(logged_in_users)
        elif choice == 5:
            print("Thank you for banking with us!")
            break
        else:
            print("Invalid choice! Please select a valid option.")
    except ValueError:
        print("Invalid input! Please choose a number between 1-5.")

output:
![Image](https://github.com/user-attachments/assets/1f708388-f766-4ea8-8585-7810b5b6c8fd)

when sometime users forget her username or password then he can use her birthdate and email id to enter the main section
![Image](https://github.com/user-attachments/assets/15948634-6246-4e44-b47b-0fbb496ecdb0)


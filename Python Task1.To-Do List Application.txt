import os
import pickle
import datetime

# Define the Transaction class
class Transaction:
    def __init__(self, category, amount, date=None):
        self.category = category
        self.amount = amount
        self.date = date if date else datetime.date.today()

# Define the BudgetTracker class
class BudgetTracker:
    def __init__(self):
        self.transactions = []

    def add_transaction(self, category, amount):
        transaction = Transaction(category, amount)
        self.transactions.append(transaction)

    def calculate_budget(self, income):
        expenses = sum(transaction.amount for transaction in self.transactions if transaction.amount < 0)
        remaining_budget = income + expenses
        return remaining_budget

    def analyze_expenses(self):
        expense_categories = set(transaction.category for transaction in self.transactions if transaction.amount < 0)
        expense_summary = {category: sum(transaction.amount for transaction in self.transactions if
                                          transaction.category == category and transaction.amount < 0)
                           for category in expense_categories}
        return expense_summary

    def save_to_file(self, filename):
        with open(filename, 'wb') as f:
            pickle.dump(self.transactions, f)

    def load_from_file(self, filename):
        if os.path.exists(filename):
            with open(filename, 'rb') as f:
                self.transactions = pickle.load(f)

# Main function
def main():
    budget_tracker = BudgetTracker()
    data_file = "transactions.pickle"

    # Load transactions from file, if it exists
    budget_tracker.load_from_file(data_file)

    while True:
        print("\n*** Budget Tracker ***")
        print("1. Add Income")
        print("2. Add Expense")
        print("3. Calculate Budget")
        print("4. Analyze Expenses")
        print("5. Save and Quit")

        choice = input("Enter your choice: ")

        if choice == '1':
            income = float(input("Enter income amount: "))
            budget_tracker.add_transaction("Income", income)
            print("Income added successfully.")

        elif choice == '2':
            category = input("Enter expense category: ")
            amount = float(input("Enter expense amount: "))
            budget_tracker.add_transaction(category, -amount)
            print("Expense added successfully.")

        elif choice == '3':
            income = float(input("Enter current total income: "))
            remaining_budget = budget_tracker.calculate_budget(income)
            print(f"Remaining budget: ${remaining_budget:.2f}")

        elif choice == '4':
            expense_summary = budget_tracker.analyze_expenses()
            for category, amount in expense_summary.items():
                print(f"Category: {category} | Total Expense: ${amount:.2f}")

        elif choice == '5':
            budget_tracker.save_to_file(data_file)
            print("Transactions saved. Goodbye!")
            break

if __name__ == "__main__":
    main()

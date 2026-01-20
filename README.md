class ExpenseSplitterAIAgent:
    def __init__(self):
        self.users = set()
        self.expenses = []
        self.balance = {}

    def add_user(self, name):
        self.users.add(name)
        self.balance[name] = 0.0

    def add_expense(self, paid_by, amount, shared_by):
        split_amount = amount / len(shared_by)

        for user in shared_by:
            self.balance[user] -= split_amount

        self.balance[paid_by] += amount
        self.expenses.append({
            "paid_by": paid_by,
            "amount": amount,
            "shared_by": shared_by
        })

    def calculate_settlements(self):
        creditors = {}
        debtors = {}

        for user, bal in self.balance.items():
            if bal > 0:
                creditors[user] = bal
            elif bal < 0:
                debtors[user] = -bal

        settlements = []

        for debtor in debtors:
            for creditor in creditors:
                if debtors[debtor] == 0:
                    break

                pay = min(debtors[debtor], creditors[creditor])
                if pay > 0:
                    settlements.append(
                        f"{debtor} pays ₹{pay:.2f} to {creditor}"
                    )
                    debtors[debtor] -= pay
                    creditors[creditor] -= pay

        return settlements

    def summary(self):
        print("\n--- Expense Summary ---")
        for user, bal in self.balance.items():
            status = "gets" if bal > 0 else "owes"
            print(f"{user} {status} ₹{abs(bal):.2f}")

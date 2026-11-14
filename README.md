import json
from pathlib import Path
from datetime import datetime

FILE = Path("deliveries.json")

def load_data():
    if FILE.exists():
        return json.loads(FILE.read_text(encoding="utf-8"))
    return []

def save_data(data):
    FILE.write_text(
        json.dumps(data, indent=4, ensure_ascii=False),
        encoding="utf-8"
    )

def add_delivery(data):
    name = input("Package name: ").strip()
    tracking = input("Tracking number: ").strip()

    if not name or not tracking:
        print("All fields are required.")
        return

    if any(item["tracking"] == tracking for item in data):
        print("Tracking number already exists.")
        return

    data.append({
        "name": name,
        "tracking": tracking,
        "status": "Processing",
        "created": str(datetime.now().date())
    })

    save_data(data)
    print("Package added successfully!")

def update_status(data):
    tracking = input("Tracking number: ").strip()

    for item in data:
        if item["tracking"] == tracking:
            print("1. Processing")
            print("2. Shipped")
            print("3. Out for delivery")
            print("4. Delivered")

            choice = input("New status: ")

            statuses = {
                "1": "Processing",
                "2": "Shipped",
                "3": "Out for delivery",
                "4": "Delivered"
            }

            if choice not in statuses:
                print("Invalid status.")
                return

            item["status"] = statuses[choice]
            save_data(data)
            print("Status updated!")
            return

    print("Package not found.")

def show_deliveries(data):
    if not data:
        print("No deliveries found.")
        return

    for item in data:
        print(f"\nPackage: {item['name']}")
        print(f"Tracking: {item['tracking']}")
        print(f"Status: {item['status']}")
        print(f"Created: {item['created']}")

def main():
    data = load_data()

    while True:
        print("\n1. Add package")
        print("2. Update status")
        print("3. Show deliveries")
        print("4. Exit")

        choice = input("Choose: ")

        if choice == "1":
            add_delivery(data)
        elif choice == "2":
            update_status(data)
        elif choice == "3":
            show_deliveries(data)
        elif choice == "4":
            break
        else:
            print("Invalid choice.")

if __name__ == "__main__":
    main()

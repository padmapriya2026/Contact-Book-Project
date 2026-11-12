
import json
import os
import re

FILE_NAME = "contacts.json"


def load_contacts():
    if os.path.exists(FILE_NAME):
        with open(FILE_NAME, "r") as file:
            return json.load(file)
    return []


def save_contacts(contacts):
    with open(FILE_NAME, "w") as file:
        json.dump(contacts, file, indent=4)


def validate_phone(phone):
    phone = re.sub(r"[\s()+-]", "", phone)
    return phone.isdigit() and 10 <= len(phone) <= 15


def validate_email(email):
    pattern = r"^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$"
    return re.match(pattern, email) is not None


def is_duplicate(contacts, phone, email, exclude_index=None):
    for index, contact in enumerate(contacts):
        if index == exclude_index:
            continue

        if (contact["phone"] == phone or
                contact["email"].lower() == email.lower()):
            return True

    return False


def add_contact(contacts):
    print("\n--- Add Contact ---")

    name = input("Enter Name: ").strip()
    phone = input("Enter Phone Number: ").strip()
    email = input("Enter Email Address: ").strip()

    if not name:
        print("Name cannot be empty!")
        return

    if not validate_phone(phone):
        print("Invalid phone number!")
        return

    if not validate_email(email):
        print("Invalid email address!")
        return

    if is_duplicate(contacts, phone, email):
        print("Duplicate contact already exists!")
        return

    contacts.append({
        "name": name,
        "phone": phone,
        "email": email
    })

    save_contacts(contacts)
    print("Contact added successfully!")


def view_contacts(contacts):
    print("\n--- All Contacts ---")

    if not contacts:
        print("No contacts available!")
        return

    for index, contact in enumerate(contacts, start=1):
        print("\nContact Number:", index)
        print("Name:", contact["name"])
        print("Phone:", contact["phone"])
        print("Email:", contact["email"])


def search_contact(contacts):
    print("\n--- Search Contact ---")

    search = input("Enter name, phone or email: ").strip().lower()

    if not search:
        print("Search field cannot be empty!")
        return

    results = [
        contact for contact in contacts
        if search in contact["name"].lower()
        or search in contact["phone"].lower()
        or search in contact["email"].lower()
    ]

    if results:
        for contact in results:
            print("\nName:", contact["name"])
            print("Phone:", contact["phone"])
            print("Email:", contact["email"])
    else:
        print("Contact not found!")


def edit_contact(contacts):
    print("\n--- Edit Contact ---")
    view_contacts(contacts)

    if not contacts:
        return

    try:
        number = int(input("\nEnter contact number to edit: "))
        index = number - 1

        if index < 0 or index >= len(contacts):
            print("Invalid contact number!")
            return

        name = input("Enter New Name: ").strip()
        phone = input("Enter New Phone: ").strip()
        email = input("Enter New Email: ").strip()

        if not name:
            print("Name cannot be empty!")
            return

        if not validate_phone(phone):
            print("Invalid phone number!")
            return

        if not validate_email(email):
            print("Invalid email address!")
            return

        if is_duplicate(contacts, phone, email, index):
            print("Duplicate contact already exists!")
            return

        contacts[index] = {
            "name": name,
            "phone": phone,
            "email": email
        }

        save_contacts(contacts)
        print("Contact updated successfully!")

    except ValueError:
        print("Please enter a valid number!")


def delete_contact(contacts):
    print("\n--- Delete Contact ---")
    view_contacts(contacts)

    if not contacts:
        return

    try:
        number = int(input("\nEnter contact number to delete: "))
        index = number - 1

        if index < 0 or index >= len(contacts):
            print("Invalid contact number!")
            return

        confirm = input("Confirm delete (yes/no): ").strip().lower()

        if confirm == "yes":
            contacts.pop(index)
            save_contacts(contacts)
            print("Contact deleted successfully!")
        else:
            print("Delete cancelled!")

    except ValueError:
        print("Please enter a valid number!")


def main():
    contacts = load_contacts()

    while True:
        print("\n===== CONTACT BOOK =====")
        print("1. Add Contact")
        print("2. View Contacts")
        print("3. Search Contact")
        print("4. Edit Contact")
        print("5. Delete Contact")
        print("6. Exit")

        choice = input("\nEnter your choice: ").strip()

        if choice == "1":
            add_contact(contacts)
        elif choice == "2":
            view_contacts(contacts)
        elif choice == "3":
            search_contact(contacts)
        elif choice == "4":
            edit_contact(contacts)
        elif choice == "5":
            delete_contact(contacts)
        elif choice == "6":
            print("Thank you for using Contact Book!")
            break
        else:
            print("Invalid choice! Please try again.")


if __name__ == "__main__":
    main()

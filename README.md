# SUPERMARKET
import datetime
import smtplib
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText


PRODUCTS = {
    "apple": 0.5,
    "banana": 0.3,
    "bread": 2.0,
    "milk": 1.5,
    "eggs": 3.0,
    "cheese": 4.0,
    "butter": 2.5,
    "yogurt": 1.0,
    "juice": 2.5,
    "water": 1.0
}

def get_product_price(product):
    
    if product in PRODUCTS:
        return PRODUCTS[product]
    else:
        raise ValueError(f"Product '{product}' not found in Meera Supermarket.")

def generate_bill(purchases):
    """Generates a bill for the given purchases."""
    total_amount = 0
    bill_details = []
    current_date = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    
    bill_details.append(f"Meera Supermarket\nDate: {current_date}\n")
    bill_details.append("Items Purchased:\n")
    
    for item, quantity in purchases.items():
        price = get_product_price(item)
        amount = price * quantity
        total_amount += amount
        bill_details.append(f"{item.capitalize()}: {quantity} x ${price:.2f} = ${amount:.2f}")
    
    bill_details.append(f"\nTotal Amount: ${total_amount:.2f}\n")
    
    return "\n".join(bill_details)

def save_bill_to_file(bill, filename):
    
    try:
        with open(filename, "w") as file:
            file.write(bill)
    except IOError as e:
        print(f"Error saving bill to file: {e}")

def send_bill_via_email(bill, recipient_email):
    
    sender_email = "your-email@gmail.com"  # Replace with your email
    sender_password = "your-email-password"  # Replace with your email password
    
    msg = MIMEMultipart()
    msg['From'] = sender_email
    msg['To'] = recipient_email
    msg['Subject'] = "Your Bill from Meera Supermarket"
    
    msg.attach(MIMEText(bill, 'plain'))
    
    try:
        server = smtplib.SMTP('smtp.gmail.com', 587)
        server.starttls()
        server.login(sender_email, sender_password)
        server.sendmail(sender_email, recipient_email, msg.as_string())
        server.quit()
        print(f"Bill sent to {recipient_email}")
    except Exception as e:
        print(f"Failed to send email: {e}")

def main():
    purchases = {}
    print("Welcome to Meera Supermarket!")
    print("Available products:", ", ".join(PRODUCTS.keys()))
    print("Enter 'done' when you are finished purchasing.\n")
    
    while True:
        try:
            product = input("Enter product name: ").strip().lower()
            if product == 'done':
                break
            
            if product not in PRODUCTS:
                print(f"Product '{product}' not found. Please enter a valid product.")
                continue
            
            quantity = int(input("Enter quantity: ").strip())
            if quantity <= 0:
                print("Quantity should be a positive integer.")
                continue
            
            if product in purchases:
                purchases[product] += quantity
            else:
                purchases[product] = quantity
        
        except ValueError as e:
            print(f"Invalid input: {e}")
    
    if not purchases:
        print("No purchases made.")
        return
    
    bill = generate_bill(purchases)
    print("\n--- Bill ---")
    print(bill)
    
    filename = "bill.txt"
    save_bill_to_file(bill, filename)
    print(f"Bill saved to {filename}")
    
    recipient_email = input("Enter your email address to receive the bill: ").strip()
    send_bill_via_email(bill, recipient_email)

if __name__ == "__main__":
    main()

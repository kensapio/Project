from datetime import datetime, timedelta
import sys

users = {
    "admin": {"password": "admin123", "role": "Admin"},
    "loki": {"password": "loki123", "role": "Staff"},
}

inventory = [
    {
        "id": 1,
        "name": "Espresso Beans",
        "unit": "kg",
        "quantity": 10,
        "min_level": 3,
        "expiration": "2026-10-15",
    },
    {
        "id": 2,
        "name": "Dairy Cream",
        "unit": "L",
        "quantity": 2,
        "min_level": 5,
        "expiration": "2026-10-05",
    },
    {
        "id": 3,
        "name": "Caramel Syrup",
        "unit": "L",
        "quantity": 4,
        "min_level": 2,
        "expiration": "2026-12-01",
    },
    {
        "id": 4,
        "name": "Fresh Milk",
        "unit": "L",
        "quantity": 1,
        "min_level": 5,
        "expiration": "2026-10-12",
    },
]
delivery_records = [
    {
        "delivery_id": 1,
        "ingredient_id": 1,
        "name": "Espresso Beans",
        "quantity": 10,
        "unit": "kg",
        "expiration": "2026-10-15",
    },
    {
        "delivery_id": 2,
        "ingredient_id": 2,
        "name": "Dairy Cream",
        "quantity": 2,
        "unit": "L",
        "expiration": "2026-10-05",
    },
    {
        "delivery_id": 3,
        "ingredient_id": 3,
        "name": "Caramel Syrup",
        "quantity": 4,
        "unit": "L",
        "expiration": "2026-12-01",
    },
    {
        "delivery_id": 4,
        "ingredient_id": 4,
        "name": "Fresh Milk",
        "quantity": 1,
        "unit": "L",
        "expiration": "2026-10-12",
    },
]

shift_handover_reports = []

def format_qty(val):
  if isinstance(val, (int, float)):
    if float(val).is_integer():
      return int(val)
  return val

def get_item_status(item):
  today = datetime.now().date()
  seven_days = today + timedelta(days=7)
  try:
    exp_date = datetime.strptime(item["expiration"], "%Y-%m-%d").date()
  except ValueError:
    return "Unknown"

  if exp_date < today:
    return "Expired"
  elif today <= exp_date <= seven_days:
    return "Expiring Soon"
  elif item["quantity"] <= item["min_level"]:
    return "Low Stock"
  else:
    return "Safe"

def validate_positive_number(prompt):
  while True:
    val_str = input(prompt).strip()
    if val_str.lower() == "back":
      return None
    try:
      val = float(val_str)
      if val <= 0:
        print("Please enter a positive number greater than 0.")
        continue
      return format_qty(val)
    except ValueError:
      print("Invalid input! Please enter a valid number or type 'back'.")

def validate_expiry_date(prompt):
  while True:
    exp_str = input(prompt).strip()
    if exp_str.lower() == "back":
      return None
    try:
      exp_date = datetime.strptime(exp_str, "%Y-%m-%d").date()
      if exp_date < datetime.now().date():
        print("Expiration date cannot be in the past!")
        continue
      return exp_str
    except ValueError:
      print("Invalid date format! Please use YYYY-MM-DD or type 'back'.")

def view_inventory():
  print("\n--- CURRENT INVENTORY ITEMS ---")
  print(
      f"{'ID':<4} | {'Ingredient Name':<20} | {'Qty':<8} | {'Unit':<6} |"
      f" {'Min':<6} | {'Expiration':<12} | {'Status'}"
  )
  print("-" * 75)
  for item in inventory:
    qty_str = str(format_qty(item["quantity"]))
    min_str = str(format_qty(item["min_level"]))
    status = get_item_status(item)
    print(
        f"{item['id']:<4} | {item['name']:<20} | {qty_str:<8} |"
        f" {item['unit']:<6} | {min_str:<6} | {item['expiration']:<12} |"
        f" {status}"
    )

def view_delivery_records():
  print("\n--- INCOMING DELIVERY / BATCH RECORDS (SEPARATE TABLE) ---")
  if not delivery_records:
    print("No deliveries have been recorded yet.")
    return
  print(
      f"{'Del. ID':<8} | {'Ing. ID':<8} | {'Ingredient Name':<20} |"
      f" {'Qty':<8} | {'Unit':<6} | {'Expiration Date'}"
  )
  print("-" * 75)
  for r in delivery_records:
    print(
        f"{r['delivery_id']:<8} | {r['ingredient_id']:<8} |"
        f" {r['name']:<20} | {format_qty(r['quantity']):<8} |"
        f" {r['unit']:<6} | {r['expiration']}"
    )

def deduct_stock_staff():
  print("\n--- DEDUCT STOCK USAGE (STAFF) ---")
  view_inventory()
  id_str = input(
      "Enter Ingredient ID used (or type 'back' to return): "
  ).strip()
  if id_str.lower() == "back":
    return
  try:
    item_id = int(id_str)
  except ValueError:
    print("Invalid input! Please enter a valid numeric ID.")
    return

  item = next((i for i in inventory if i["id"] == item_id), None)

  if not item:
    print("Ingredient ID not found.")
    return

  print(
      f"Selected: {item['name']} | Current Stock:"
      f" {format_qty(item['quantity'])} {item['unit']}"
  )
  amt = validate_positive_number(
      f"Enter quantity used/consumed ({item['unit']}) or 'back': "
  )
  if amt is None:
    return

  if amt > item["quantity"]:
    print(
        f"Insufficient stock! Only {format_qty(item['quantity'])}"
        f" {item['unit']} available."
    )
  else:
    item["quantity"] = format_qty(item["quantity"] - amt)
    print(
        f"Deducted {format_qty(amt)} {item['unit']} from {item['name']}."
        f" Remaining Stock: {format_qty(item['quantity'])} {item['unit']}"
    )

def adjust_stock_admin():
  while True:
    print("\n--- STOCK ADJUSTMENT (ADMIN) ---")
    view_inventory()
    id_str = input(
        "Enter Ingredient ID to adjust (or type 'back' to return): "
    ).strip()
    if id_str.lower() == "back":
      break
    try:
      item_id = int(id_str)
    except ValueError:
      print("Invalid input! Please enter a valid numeric ID.")
      continue

    item = next((i for i in inventory if i["id"] == item_id), None)

    if not item:
      print("Ingredient ID not found.")
      continue

    print(
        f"Selected: {item['name']} | Current Stock:"
        f" {format_qty(item['quantity'])} {item['unit']}"
    )
    print("1. Add Stock (+)")
    print("2. Deduct Stock (-)")
    print("3. Back")
    action = input("Select action (1-3): ").strip()

    if action == "1":
      amt = validate_positive_number(
          f"Enter quantity to add ({item['unit']}) or 'back': "
      )
      if amt is None:
        continue
      item["quantity"] = format_qty(item["quantity"] + amt)
      print(
          f"Added stock to {item['name']}. Updated Quantity:"
          f" {format_qty(item['quantity'])} {item['unit']}"
      )
    elif action == "2":
      amt = validate_positive_number(
          f"Enter quantity to deduct ({item['unit']}) or 'back': "
      )
      if amt is None:
        continue
      if amt > item["quantity"]:
        print(
            f"Cannot deduct more than current stock! Only"
            f" {format_qty(item['quantity'])} {item['unit']} remaining."
        )
      else:
        item["quantity"] = format_qty(item["quantity"] - amt)
        print(
            f"Deducted stock from {item['name']}. Updated Quantity:"
            f" {format_qty(item['quantity'])} {item['unit']}"
        )
    elif action == "3":
      break
    else:
      print("Invalid selection.")
    break

def record_delivery():
  print("\n--- RECORD INCOMING DELIVERY ---")
  view_inventory()
  id_str = input(
      "Enter Ingredient ID for delivery (or type 'back' to return): "
  ).strip()
  if id_str.lower() == "back":
    return
  try:
    item_id = int(id_str)
  except ValueError:
    print("Invalid input! Please enter a valid numeric ID.")
    return

  item = next((i for i in inventory if i["id"] == item_id), None)

  if item:
    delivery_qty = validate_positive_number(
        f"Enter delivered quantity ({item['unit']}) or 'back': "
    )
    if delivery_qty is None:
      return
    new_exp = validate_expiry_date(
        "Enter new batch expiration date (YYYY-MM-DD) or 'back': "
    )
    if new_exp is None:
      return

    item["quantity"] = format_qty(item["quantity"] + delivery_qty)
    item["expiration"] = (
        new_exp
    )

    new_del_id = (
        max([d["delivery_id"] for d in delivery_records], default=0) + 1
    )
    delivery_records.append({
        "delivery_id": new_del_id,
        "ingredient_id": item["id"],
        "name": item["name"],
        "quantity": delivery_qty,
        "unit": item["unit"],
        "expiration": new_exp,
    })

    print(
        f"Delivery successfully recorded in separate table! New total for"
        f" '{item['name']}': {format_qty(item['quantity'])} {item['unit']}"
    )
  else:
    print("Ingredient ID not found.")

def remove_ingredient_admin():
  print("\n--- REMOVE INGREDIENT ---")
  view_inventory()
  id_str = input(
      "Enter Ingredient ID to remove (or type 'back' to return): "
  ).strip()
  if id_str.lower() == "back":
    return
  try:
    item_id = int(id_str)
  except ValueError:
    print("Invalid input! Please enter a valid numeric ID.")
    return

  global inventory
  initial_count = len(inventory)
  removed_name = next((i["name"] for i in inventory if i["id"] == item_id), "")
  inventory = [i for i in inventory if i["id"] != item_id]

  if len(inventory) < initial_count:
    print(
        f"Successfully removed '{removed_name}' (ID: {item_id}) from inventory."
    )
  else:
    print("Ingredient ID not found.")

def stock_filter_menu():
  today = datetime.now().date()
  seven_days = today + timedelta(days=7)

  while True:
    print("\n------------------------------------------")
    print("         FILTER & CRITICAL STOCK REPORTS          ")
    print("------------------------------------------")
    print("1. View Critical Low Stock Items")
    print("2. View Expired Items")
    print("3. View Near Expiry Items (Next 7 Days)")
    print("4. View All Critical Alerts Combined")
    print("5. Back to Dashboard")

    choice = input("Select filter option (1-5): ").strip()

    if choice == "1":
      print("\n--- CRITICAL LOW STOCK ITEMS ---")
      low_items = [i for i in inventory if i["quantity"] <= i["min_level"]]
      if not low_items:
        print("All stock levels are healthy.")
      else:
        for i in low_items:
          print(
              f"ID {i['id']} - {i['name']}: {format_qty(i['quantity'])}"
              f" {i['unit']} remaining (Min Target:"
              f" {format_qty(i['min_level'])})"
          )

    elif choice == "2":
      print("\n--- EXPIRED ITEMS ---")
      expired_items = [
          i
          for i in inventory
          if datetime.strptime(i["expiration"], "%Y-%m-%d").date() < today
      ]
      if not expired_items:
        print("No expired items found in inventory.")
      else:
        for i in expired_items:
          print(
              f"ID {i['id']} - {i['name']} - Expired on: {i['expiration']}"
              f" (Qty: {format_qty(i['quantity'])} {i['unit']})"
          )

    elif choice == "3":
      print("\n--- NEAR EXPIRY ITEMS (WITHIN 7 DAYS) ---")
      near_exp = [
          i
          for i in inventory
          if today <= datetime.strptime(i["expiration"], "%Y-%m-%d").date()
          <= seven_days
      ]
      if not near_exp:
        print("No items expiring within the next 7 days.")
      else:
        for i in near_exp:
          print(
              f"ID {i['id']} - {i['name']} - Expiration: {i['expiration']}"
              f" (Qty: {format_qty(i['quantity'])} {i['unit']})"
          )

    elif choice == "4":
      print("\n--- SUMMARY OF ALL CRITICAL ALERTS ---")
      low_items = [i for i in inventory if i["quantity"] <= i["min_level"]]
      expired_items = [
          i
          for i in inventory
          if datetime.strptime(i["expiration"], "%Y-%m-%d").date() < today
      ]
      near_exp = [
          i
          for i in inventory
          if today <= datetime.strptime(i["expiration"], "%Y-%m-%d").date()
          <= seven_days
      ]

      print(
          f"\nLow Stock: {len(low_items)} items | • Expired:"
          f" {len(expired_items)} items | • Near Expiry: {len(near_exp)} items"
      )
      print("-" * 65)

      if expired_items:
        print("\n[ EXPIRED ]")
        for i in expired_items:
          print(f" - ID {i['id']} - {i['name']}: {i['expiration']}")

      if low_items:
        print("\n[ LOW STOCK ]")
        for i in low_items:
          print(
              f" - ID {i['id']} - {i['name']}:"
              f" {format_qty(i['quantity'])}/{format_qty(i['min_level'])}"
              f" {i['unit']}"
          )

      if near_exp:
        print("\n[ NEAR EXPIRY ]")
        for i in near_exp:
          print(f" - ID {i['id']} - {i['name']}: {i['expiration']}")

      if not (low_items or expired_items or near_exp):
        print("Complete inventory is healthy!")

    elif choice == "5":
      break
    else:
      print("Invalid option. Try again.")

def staff_handover(username):
  print("\n--- LOG SHIFT HANDOVER REPORT ---")
  usage = (
      input(
          "Enter ingredient usage summary during shift (or type 'back'): "
      ).strip()
  )
  if usage.lower() == "back":
    return
  notes = input("Enter any notes/issues for the next shift: ").strip()

  shift_handover_reports.append(
      {"staff": username, "usage": usage, "notes": notes}
  )
  print("Shift handover report successfully logged!")

def admin_dashboard(username):
  global inventory
  while True:
    print("\n==========================================")
    print(f"  LOKI CAFÉ - ADMIN DASHBOARD ({username})  ")
    print("==========================================")
    print("1. View All Stock Levels")
    print("2. View Delivery Records Table)")
    print("3. Add New Ingredient")
    print("4. Stock Adjustment")
    print("5. Remove Ingredient")
    print("6. Record Incoming Delivery")
    print("7. Filter Stock Alert")
    print("8. View Shift Handover Reports")
    print("9. Register New Staff Account")
    print("10. Logout")

    choice = input("Select option (1-10): ").strip()

    if choice == "1":
      view_inventory()
    elif choice == "2":
      view_delivery_records()
    elif choice == "3":
      print("\n--- ADD NEW INGREDIENT ---")
      name = input(
          "Enter Ingredient Name (or type 'back' to return): "
      ).strip()
      if name.lower() == "back":
        continue
      name = name.title()

      if any(i["name"].lower() == name.lower() for i in inventory):
        print(
            f"'{name}' already exists in inventory! Use Stock Adjustment"
            " instead."
        )
        continue

      unit = input("Enter Unit of Measure (e.g., kg, L, pcs): ").strip()
      qty = validate_positive_number("Enter Initial Quantity or 'back': ")
      if qty is None:
        continue
      min_lvl = validate_positive_number(
          "Enter Minimum Alert Level or 'back': "
      )
      if min_lvl is None:
        continue
      exp = validate_expiry_date(
          "Enter Expiration Date (YYYY-MM-DD) or 'back': "
      )
      if exp is None:
        continue

      new_id = max([i["id"] for i in inventory], default=0) + 1

      inventory.append({
          "id": new_id,
          "name": name,
          "unit": unit,
          "quantity": qty,
          "min_level": min_lvl,
          "expiration": exp,
      })
      
      new_del_id = (
          max([d["delivery_id"] for d in delivery_records], default=0) + 1
      )
      delivery_records.append({
          "delivery_id": new_del_id,
          "ingredient_id": new_id,
          "name": name,
          "quantity": qty,
          "unit": unit,
          "expiration": exp,
      })

      print(
          f"Successfully added '{name}' (ID: {new_id}) to inventory and delivery"
          " records!"
      )

    elif choice == "4":
      adjust_stock_admin()

    elif choice == "5":
      remove_ingredient_admin()

    elif choice == "6":
      record_delivery()

    elif choice == "7":
      stock_filter_menu()

    elif choice == "8":
      print("\n--- SHIFT HANDOVER REPORTS ---")
      if not shift_handover_reports:
        print("No reports logged yet.")
      else:
        for idx, r in enumerate(shift_handover_reports, 1):
          print(
              f"\nReport #{idx}\nStaff:"
              f" {r['staff']}\nUsage:\n{r['usage']}\nNotes:\n{r['notes']}"
          )

    elif choice == "9":
      print("\n--- REGISTER STAFF ACCOUNT ---")
      new_user = input(
          "Enter New Staff Username (or type 'back'): "
      ).strip().lower()
      if new_user == "back":
        continue
      if new_user in users:
        print("Username already exists!")
      else:
        new_pass = input("Enter New Staff Password: ").strip()
        users[new_user] = {"password": new_pass, "role": "Staff"}
        print(f"Successfully registered new staff account: {new_user}")

    elif choice == "10":
      print("Logging out from Admin Dashboard...")
      break
    else:
      print("Invalid option. Please try again.")

def staff_dashboard(username):
  while True:
    print("\n==========================================")
    print(f"  LOKI CAFÉ - STAFF DASHBOARD ({username})  ")
    print("==========================================")
    print("1. View All Stock Levels")
    print("2. View Delivery Records Table)")
    print("3. Deduct Stock Usage")
    print("4. Record Incoming Delivery")
    print("5. Filter Stock Alerts")
    print("6. Log Shift Handover Report")
    print("7. Logout")

    choice = input("Select option (1-7): ").strip()

    if choice == "1":
      view_inventory()
    elif choice == "2":
      view_delivery_records()
    elif choice == "3":
      deduct_stock_staff()
    elif choice == "4":
      record_delivery()
    elif choice == "5":
      stock_filter_menu()
    elif choice == "6":
      staff_handover(username)
    elif choice == "7":
      print("Logging out from Staff Dashboard...")
      break
    else:
      print("Invalid option. Please try again.")

def main():
  while True:
    print("\n==========================================")
    print("    LOKI CAFÉ - STOCK INVENTORY SYSTEM    ")
    print("==========================================")
    print("1. Login")
    print("2. Exit System")

    choice = input("Select option (1-2): ").strip()

    if choice == "1":
      print("\n--- SYSTEM LOGIN ---")
      uname = input("Username: ").strip().lower()
      pwd = input("Password: ").strip()

      if uname in users and users[uname]["password"] == pwd:
        role = users[uname]["role"]
        print(f"\nLogin successful! Welcome, {uname} ({role})")

        if role == "Admin":
          admin_dashboard(uname)
        elif role == "Staff":
          staff_dashboard(uname)
      else:
        print("Invalid username or password!")

    elif choice == "2":
      print("\nExiting Loki Café Inventory System. Goodbye!")
      sys.exit()
    else:
      print("Invalid selection. Please choose 1-2.")

main()

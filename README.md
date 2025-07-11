# PDF-using-python
This is a auto script in which create the PDF like Shop price and manage shop item
GNU nano 8.4                                            pdf.py
import re
import os
from fpdf import FPDF
from datetime import datetime

customer_name = input("🧾 Enter your Shopping Amount: ")
pdf_file_name = input("💾 PDF File Name (without .pdf): ") + ".pdf"

item_list = []
sno = 1

print("\n👉 Enter shopping items (type 'done' to finish):")
while True:
    item_name = input(f"\nItem {sno} Name: ")
    if item_name.strip().lower() == 'done':
        break

    quantity = input("Quantity : ")
    amount = input("Total Amount (Rs.): ")
    date = input("Date: ")

    if not date.strip():
        date = datetime.today().strftime("%d %B %Y")

    try:
        amount_float = float(amount)

        match = re.match(r"([\d\.]+)\s*(kg|k|g|l|liter|litre|ml|dozen|pack)?", quantity.lower())
        if match:
            number = float(match.group(1))
            unit = match.group(2)

            if unit == "g":
                quantity_val = number / 1000
            elif unit == "ml":
                quantity_val = number / 1000
            elif unit in ["kg", "k", "l", "liter", "litre", "dozen", "pack", None]:
                quantity_val = number
            else:
                quantity_val = 1

            rate = amount_float / quantity_val if quantity_val != 0 else 0
        else:
            rate = amount_float
            quantity_val = 1

    except ValueError:
        print("❌ Invalid input! Try again.")
        continue

    item_list.append([
        sno,
        item_name,
        quantity,
        f"Rs.{rate:.2f}",
        f"Rs.{amount_float:.2f}",
        date
    ])
    sno += 1


pdf = FPDF()
pdf.add_page()
pdf.set_font("Arial", 'B', 16)

pdf.cell(200, 10, txt="Shopping Bill", ln=True, align='C')
pdf.set_font("Arial", '', 12)
pdf.cell(200, 10, txt=f"Shooping Amount: {customer_name}", ln=True, align='C')
pdf.ln(10)

pdf.set_font("Arial", 'B', 12)
headers = ["S.No.", "Item Name", "Quantity", "Rate (Rs.)", "Amount (Rs.)", "Date"]
col_widths = [15, 40, 30, 30, 30, 45]

for i, header in enumerate(headers):
    pdf.cell(col_widths[i], 10, header, 1, 0, 'C')
pdf.ln()

pdf.set_font("Arial", '', 12)
for row in item_list:
    for i, item in enumerate(row):
        pdf.cell(col_widths[i], 10, str(item), 1, 0, 'C')
    pdf.ln()

total_amount = sum(float(row[4].replace("Rs.", "")) for row in item_list)

pdf.ln(5)
pdf.set_font("Arial", 'B', 12)
pdf.cell(0, 10, f"Total Amount: Rs.{total_amount:.2f}", ln=True)

pdf.ln(5)
pdf.set_font("Arial", 'I', 10)

pdf.output(pdf_file_name)
move_command = f"mv '{pdf_file_name}' /storage/emulated/0"
os.system(move_command)

print(f"\n✅ File saved as '{pdf_file_name}' in internal storage.")

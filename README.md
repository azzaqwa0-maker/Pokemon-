import tkinter as tk
from tkinter import messagebox
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

stok_unknown = 1
transaksi = []

def tambah_kartu():
    global stok_unknown

    tipe = var_tipe.get()

    if tipe == "":
        messagebox.showwarning("Peringatan", "Pilih tipe kartu dulu!")
        return

    if tipe == "basic":
        harga = 10000
        transaksi.append(("Basic", harga))

    elif tipe == "premium":
        harga = 50000
        transaksi.append(("Premium", harga))

    elif tipe == "unknown":
        if stok_unknown > 0:
            harga = 10000000
            stok_unknown -= 1
            transaksi.append(("UNKNOWN (LIMITED)", harga))
        else:
            messagebox.showinfo("Habis", "Kartu UNKNOWN sudah habis!")
            return

    listbox.insert(tk.END, f"{transaksi[-1][0]} - Rp{transaksi[-1][1]:,}")
    total.set(total.get() + harga)

def reset():
    global stok_unknown, transaksi
    listbox.delete(0, tk.END)
    total.set(0)
    stok_unknown = 1
    transaksi = []

def checkout():
    if not transaksi:
        messagebox.showwarning("Kosong", "Belum ada pembelian!")
        return

    file_name = "struk_pokemon.pdf"
    c = canvas.Canvas(file_name, pagesize=letter)

    c.setFont("Helvetica-Bold", 16)
    c.drawString(180, 750, "STRUK TOKO KARTU POKEMON")

    c.setFont("Helvetica", 12)
    y = 700

    total_harga = 0

    for i, item in enumerate(transaksi, start=1):
        nama, harga = item
        c.drawString(50, y, f"{i}. {nama} - Rp{harga:,}")
        y -= 20
        total_harga += harga

    c.setFont("Helvetica-Bold", 12)
    c.drawString(50, y - 20, f"TOTAL: Rp{total_harga:,}")

    c.save()

    messagebox.showinfo("Checkout", f"Struk berhasil dibuat!\n{file_name}")

# === GUI ===
root = tk.Tk()
root.title("Toko Kartu Pokémon")
root.geometry("420x500")

tk.Label(root, text="TOKO KARTU POKEMON", font=("Arial", 16, "bold")).pack(pady=10)

var_tipe = tk.StringVar()

tk.Label(root, text="Pilih tipe kartu:").pack()
tk.Radiobutton(root, text="Basic (Rp10.000)", variable=var_tipe, value="basic").pack()
tk.Radiobutton(root, text="Premium (Rp50.000)", variable=var_tipe, value="premium").pack()
tk.Radiobutton(root, text="Unknown (Rp10.000.000 - 1x)", variable=var_tipe, value="unknown").pack()

tk.Button(root, text="Tambah Kartu", command=tambah_kartu).pack(pady=10)

listbox = tk.Listbox(root, width=50)
listbox.pack(pady=10)

total = tk.IntVar()
total.set(0)

tk.Label(root, text="Total Harga:").pack()
tk.Label(root, textvariable=total, font=("Arial", 14, "bold")).pack()

tk.Button(root, text="Checkout (Buat PDF)", command=checkout).pack(pady=5)
tk.Button(root, text="Reset", command=reset).pack(pady=5)

root.mainloop()

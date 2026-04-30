import tkinter as tk
from tkinter import ttk, messagebox, filedialog
import json, os, csv, datetime

# =========================
# DATABASE
# =========================
DB_FILE = "ayamku_db.json"

def load_db():
    if not os.path.exists(DB_FILE):
        return {"kandang": [], "pengiriman": []}
    with open(DB_FILE, "r") as f:
        return json.load(f)

def save_db():
    with open(DB_FILE, "w") as f:
        json.dump(DB, f, indent=2)

DB = load_db()

# =========================
# USERS
# =========================
USERS = {
    "admin": {"password": "4dm1n", "role": "admin"},
    "krwn": {"password": "k4ry4w4n", "role": "karyawan"}
}

# =========================
# ROOT
# =========================
root = tk.Tk()
root.title("AyamKu Enterprise 2026")
root.geometry("1000x650")
root.configure(bg="#0d1117")

style = ttk.Style()
style.theme_use("default")
style.configure("Treeview",
    background="#161b22",
    foreground="white",
    fieldbackground="#161b22",
    rowheight=28)
style.map("Treeview", background=[("selected", "#f0a500")])

# =========================
# UTIL
# =========================
def clear():
    for w in root.winfo_children():
        w.destroy()

def header(text):
    tk.Label(root, text=text, fg="#f0a500", bg="#0d1117",
             font=("Arial", 20, "bold")).pack(pady=10)

def now():
    return datetime.datetime.now().strftime("%Y-%m-%d %H:%M")

# =========================
# LOGIN
# =========================
def login_screen():
    clear()
    header("🐔 AyamKu Enterprise 2026")

    f = tk.Frame(root, bg="#0d1117")
    f.pack(pady=50)

    user = tk.Entry(f, bg="#161b22", fg="white")
    pw   = tk.Entry(f, show="*", bg="#161b22", fg="white")

    user.grid(row=0, column=0, pady=5)
    pw.grid(row=1, column=0, pady=5)

    def login():
        u = USERS.get(user.get())
        if not u or u["password"] != pw.get():
            messagebox.showerror("Error", "Login salah")
            return
        if u["role"] == "admin":
            admin_dashboard()
        else:
            karyawan_menu()

    tk.Button(f, text="Login", bg="#f0a500", command=login).grid(row=2, column=0, pady=10)

# =========================
# ADMIN DASHBOARD
# =========================
def admin_dashboard():
    clear()
    header("👑 Dashboard Admin")

    kandang = DB["kandang"]
    pengiriman = DB["pengiriman"]

    total_hidup = kandang[-1]["hidup"] if kandang else 0
    total_mati = sum(x["mati"] for x in kandang)
    total_kirim = sum(x["jumlah"] for x in pengiriman)

    tk.Label(root, text=f"Hidup: {total_hidup} | Mati: {total_mati} | Kirim: {total_kirim}",
             fg="white", bg="#0d1117").pack()

    tree = ttk.Treeview(root,
        columns=("tgl","hidup","mati"),
        show="headings")

    tree.heading("tgl", text="Tanggal")
    tree.heading("hidup", text="Hidup")
    tree.heading("mati", text="Mati")

    for k in kandang:
        tree.insert("", "end",
            values=(k["tgl"], k["hidup"], k["mati"]))

    tree.pack(fill="both", expand=True, padx=20, pady=20)

    def export_csv():
        file = filedialog.asksaveasfilename(defaultextension=".csv")
        if not file: return

        with open(file, "w", newline="") as f:
            w = csv.writer(f)
            w.writerow(["Tanggal","Hidup","Mati"])
            for k in kandang:
                w.writerow([k["tgl"], k["hidup"], k["mati"]])

        messagebox.showinfo("OK", "Export berhasil!")

    tk.Button(root, text="Export CSV", command=export_csv).pack(pady=5)
    tk.Button(root, text="Refresh", command=admin_dashboard).pack()
    tk.Button(root, text="Logout", command=login_screen, bg="red").pack(pady=10)

# =========================
# KARYAWAN
# =========================
def karyawan_menu():
    clear()
    header("🧑‍🌾 Menu Karyawan")

    tk.Button(root, text="Input Kandang", width=20,
              command=input_kandang).pack(pady=5)

    tk.Button(root, text="Input Pengiriman", width=20,
              command=input_pengiriman).pack(pady=5)

    tk.Button(root, text="Logout", command=login_screen,
              bg="red").pack(pady=10)

# =========================
# INPUT KANDANG
# =========================
def input_kandang():
    clear()
    header("Input Kandang")

    hidup = tk.Entry(root)
    mati  = tk.Entry(root)

    hidup.pack(pady=5)
    mati.pack(pady=5)

    def save():
        try:
            h = int(hidup.get())
            m = int(mati.get())
        except:
            messagebox.showerror("Error", "Harus angka")
            return

        DB["kandang"].append({
            "tgl": now(),
            "hidup": h,
            "mati": m
        })
        save_db()
        messagebox.showinfo("OK", "Tersimpan")
        karyawan_menu()

    tk.Button(root, text="Simpan", bg="#f0a500", command=save).pack(pady=10)
    tk.Button(root, text="Kembali", command=karyawan_menu).pack()

# =========================
# INPUT PENGIRIMAN
# =========================
def input_pengiriman():
    clear()
    header("Input Pengiriman")

    jumlah = tk.Entry(root)
    jumlah.pack(pady=5)

    def save():
        try:
            j = int(jumlah.get())
        except:
            messagebox.showerror("Error", "Harus angka")
            return

        DB["pengiriman"].append({
            "tgl": now(),
            "jumlah": j
        })
        save_db()
        messagebox.showinfo("OK", "Tersimpan")
        karyawan_menu()

    tk.Button(root, text="Simpan", bg="#3fb950", command=save).pack(pady=10)
    tk.Button(root, text="Kembali", command=karyawan_menu).pack()

# =========================
# START
# =========================
login_screen()
root.mainloop()

# super-duper-spork
Итоговая аттестация
import tkinter as tk
from tkinter import ttk, messagebox
import json
import os
import random

# Константы
HISTORY_FILE = 'history.json'

# Функция для загрузки истории
def load_history():
    if os.path.exists(HISTORY_FILE):
        with open(HISTORY_FILE, 'r') as f:
            return json.load(f)
    return []

# Функция для сохранения истории
def save_to_history(password):
    history = load_history()
    history.append(password)
    with open(HISTORY_FILE, 'w') as f:
        json.dump(history, f)

# Генерация пароля
def generate_password():
    length = password_length.get()
    if length < 4 or length > 64:
        messagebox.showerror("Ошибка", "Длина пароля должна быть от 4 до 64")
        return
    chars = ''
    if digits_var.get():
        chars += '0123456789'
    if letters_var.get():
        chars += 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ'
    if symbols_var.get():
        chars += '!@#$%^&*()-_=+[]{}|;:,.<>?'
    if not chars:
        messagebox.showerror("Ошибка", "Выберите хотя бы один тип символов")
        return
    password = ''.join(random.choice(chars) for _ in range(length))
    password_entry.delete(0, tk.END)
    password_entry.insert(0, password)
    save_to_history(password)
    update_history()

# Обновление таблицы истории
def update_history():
    for row in tree.get_children():
        tree.delete(row)
    for pw in load_history():
        tree.insert('', tk.END, values=(pw,))

# Настройка интерфейса
root = tk.Tk()
root.title("Random Password Generator")

# Ползунок длины пароля
tk.Label(root, text="Длина пароля:").grid(row=0, column=0, sticky='w')
password_length = tk.IntVar(value=12)
length_scale = tk.Scale(root, from_=4, to=64, orient='horizontal', variable=password_length)
length_scale.grid(row=0, column=1, sticky='we')

# Чекбоксы для типа символов
digits_var = tk.BooleanVar(value=True)
letters_var = tk.BooleanVar(value=True)
symbols_var = tk.BooleanVar(value=False)

tk.Checkbutton(root, text="Цифры", variable=digits_var).grid(row=1, column=0, sticky='w')
tk.Checkbutton(root, text="Буквы", variable=letters_var).grid(row=1, column=1, sticky='w')
tk.Checkbutton(root, text="Спецсимволы", variable=symbols_var).grid(row=1, column=2, sticky='w')

# Кнопка генерации
generate_btn = tk.Button(root, text="Генерировать", command=generate_password)
generate_btn.grid(row=2, column=0, columnspan=3, pady=10)

# Поле для отображения пароля
tk.Label(root, text="Пароль:").grid(row=3, column=0, sticky='w')
password_entry = tk.Entry(root, width=50)
password_entry.grid(row=3, column=1, columnspan=2, sticky='we')

# Таблица истории
tk.Label(root, text="История паролей:").grid(row=4, column=0, sticky='w')
columns = ('Password',)
tree = ttk.Treeview(root, columns=columns, show='headings')
tree.heading('Password', text='Пароль')
tree.grid(row=5, column=0, columnspan=3, sticky='nsew')

# Настройка растяжения
root.grid_rowconfigure(5, weight=1)
root.grid_columnconfigure(1, weight=1)

update_history()

root.mainloop()

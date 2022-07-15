# imports
import os
import tkinter
from tkinter import *
from tkinter import StringVar
from PIL import ImageTk, Image
# Main Screen
win = tkinter.Tk()
win.title('SoCash')
# Set the size of window
win.geometry("350x400")
# Set the background
win.configure(bg='gold')


# Functions
def finish_reg():
    name = temp_name.get()
    age = temp_age.get()
    gender = temp_gender.get()
    password = temp_password.get()
    all_accounts = os.listdir()

    if name == "" or age == "" or gender == "" or password == "":
        notif.config(fg='red', text='All fields are required*')
        return
    for name_check in all_accounts:
        if name == name_check or password != password:
            notif.config(fg='red', text='Account already exists')
            return
        else:
            new_file = open(name, 'w')
            new_file.write(name+'\n')
            new_file.write(age+'\n')
            new_file.write(gender+'\n')
            new_file.write(password+'\n')
            new_file.write('0')
            new_file.close()
            notif.config(fg='green', text='Account created successfully')


# noinspection PyGlobalUndefined
def register():
    # Variables
    global temp_name
    global temp_age
    global temp_gender
    global temp_password
    global notif
    temp_name = StringVar()
    temp_age = StringVar()
    temp_gender = StringVar()
    temp_password = StringVar()
    # Register screen
    register_screen = Toplevel(win)
    register_screen.configure(bg='gold')
    register_screen.title('Register')
    # Labels and Entries
    Label(register_screen, text='Please fill in your details bellow to register', font='georgia 10 bold', bg='gold').grid(sticky='w', row=0, column=0, pady=5, padx=5)
    Label(register_screen, text='Name: ', font='georgia 10', bg='gold').grid(row=1, sticky='w', pady=5, padx=5)
    Label(register_screen, text='Age: ', font='georgia 10', bg='gold').grid(row=2, sticky='w', pady=5, padx=5)
    Label(register_screen, text='Gender: ', font='georgia 10', bg='gold').grid(row=3, sticky='w', pady=5, padx=5)
    Label(register_screen, text='Password: ', font='georgia 10', bg='gold').grid(row=4, sticky='w', pady=5, padx=5)
    notif = Label(register_screen, font='georgia 10', bg='gold')
    notif.grid(row=6, column=0, sticky=N)
    # Entries
    Entry(register_screen, textvariable=temp_name,  bd=5).grid(row=1, column=1, sticky='w', padx=5, pady=5)
    Entry(register_screen, textvariable=temp_age,  bd=5).grid(row=2, column=1, sticky='w', padx=5, pady=5)
    Entry(register_screen, textvariable=temp_gender,  bd=5).grid(row=3, column=1, sticky='w', padx=5, pady=5)
    Entry(register_screen, textvariable=temp_password,  bd=5, show="*").grid(row=4, column=1, sticky='w', padx=5, pady=5)
    # Buttons
    Button(register_screen, text='Register', command=finish_reg, font='georgia 10 bold', bg='white').grid(row=5, sticky=N)


def login_session():
    # Variables
    global login_name
    all_accounts = os.listdir()
    login_name = temp_login_name.get()
    login_password = temp_login_password.get()

    for name in all_accounts:
        if name == login_name:
            file = open(name, 'r')
            file_data = file.read()
            file_data = file_data.split('\n')
            password = file_data[3]
            # Account Dashboard
            if login_password == password:
                login_screen.destroy()
                account_dashboard = Toplevel(win)
                account_dashboard.title('Dashboard')
                account_dashboard.configure(bg='gold')
                # Labels
                Label(account_dashboard, text='Welcome '+name, font='georgia 10 bold', bg='gold').grid(row=0, sticky=N, pady=10)
                Label(account_dashboard, text='Account dashboard', font='georgia 10 bold', bg='gold').grid(row=1, sticky=N, padx=5)
                # Buttons
                Button(account_dashboard, text='Personal details', font='georgia 11 italic', bg='gold', width=20, command=personal_details).grid(row=2, sticky=N, padx=10, pady=5)
                Button(account_dashboard, text='Deposit', font='georgia 11 italic', bg='gold', width=20, command=deposit).grid(row=3, sticky=N, padx=10, pady=5)
                Button(account_dashboard, text='Withdraw', font='georgia 11 italic', bg='gold', width=20, command=withdraw).grid(row=4, sticky=N, padx=10, pady=5)
                Label(account_dashboard, bg='gold').grid(row=5, sticky=N, pady=5)
                return
            else:
                login_notif.config(fg='red', bg='gold', text='Incorrect password*')
            return
    login_notif.config(fg='red', bg='gold', text='Account does not exist*')


def deposit():
    # Variables
    global amount
    global deposit_notif
    global current_balance_label
    amount = StringVar()
    file = open(login_name, 'r')
    file_data = file.read()
    user_details = file_data.split('\n')
    details_balance = user_details[4]
    # Deposit screen
    deposit_screen = Toplevel(win)
    deposit_screen.title('Deposit')
    deposit_screen.configure(bg='gold')
    # Labels
    Label(deposit_screen, text='Deposit ', font='georgia 12 bold italic', bg='gold').grid(row=0, sticky=N, pady=10, padx=5)
    current_balance_label = Label(deposit_screen, text='Current Balance :$ '+details_balance, font='georgia 10', bg='gold')
    current_balance_label.grid(row=1, sticky='w', padx=5, pady=5)
    Label(deposit_screen, text='Amount : ', font='georgia 10', bg='gold').grid(row=2, sticky=W, padx=5, pady=5)
    deposit_notif = Label(deposit_screen, font='georgia 10', bg='gold')
    deposit_notif.grid(row=4, sticky='w', pady=5, padx=5)
    # Entry
    Entry(deposit_screen, textvariable=amount).grid(row=2, column=1, pady=5, padx=5)
    # Button
    Button(deposit_screen, text='OK', font='georgia 10', bg='gold', command=finish_deposit).grid(row=3, sticky=W, pady=5, padx=5)


def finish_deposit():
    if amount.get() == "":
        deposit_notif.config(text='Please fill in amount*', font='georgia 10', bg='gold', fg='red')
        return
    if float(amount.get()) <= 0:
        deposit_notif.config(text='Please fill amount higher than 0KSHS*', font='georgia 10', bg='gold', fg='red')
        return
    # Variables
    file = open(login_name, 'r+')
    file_data = file.read()
    details = file_data.split('\n')
    current_balance = details[4]
    updated_balance = current_balance
    updated_balance = float(updated_balance) + float(amount.get())
    file_data = file_data.replace(current_balance, str(updated_balance))
    file.seek(0)
    file.truncate(0)
    file.write(file_data)
    file.close()

    current_balance_label.config(text='Current balance :$'+str(updated_balance), fg='green')
    deposit_notif.config(text='Balance updated', fg='green', bg='gold')


def withdraw():
    # Variables
    global withdraw_amount
    global withdraw_notif
    global current_balance_label
    withdraw_amount = StringVar()
    file = open(login_name, 'r')
    file_data = file.read()
    user_details = file_data.split('\n')
    details_balance = user_details[4]
    # Withdraw screen
    withdraw_screen = Toplevel(win)
    withdraw_screen.title('Withdraw')
    withdraw_screen.configure(bg='gold')
    # Labels
    Label(withdraw_screen, text='Withdraw ', font='georgia 12 bold italic', bg='gold').grid(row=0, sticky=N, pady=10, padx=5)
    current_balance_label = Label(withdraw_screen, text='Current Balance :$ ' + details_balance, font='georgia 10', bg='gold')
    current_balance_label.grid(row=1, sticky='w', padx=5, pady=5)
    Label(withdraw_screen, text='Amount : ', font='georgia 10', bg='gold').grid(row=2, sticky=W, padx=5, pady=5)
    withdraw_notif = Label(withdraw_screen, font='georgia 10', bg='gold')
    withdraw_notif.grid(row=4, sticky='w', pady=5, padx=5)
    # Entry
    Entry(withdraw_screen, textvariable=withdraw_amount).grid(row=2, column=1, pady=5, padx=5)
    # Button
    Button(withdraw_screen, text='OK', font='georgia 10', bg='gold', command=finish_withdraw).grid(row=3, sticky=W, pady=5, padx=5)


def finish_withdraw():
    if withdraw_amount.get() == "":
        withdraw_notif.config(text='Please fill in amount*', font='georgia 10', bg='gold', fg='red')
        return
    if float(withdraw_amount.get()) <= 0:
        withdraw_notif.config(text='Please fill in a valid amount', font='georgia 10', bg='gold', fg='red')
        return
    # Variables
    file = open(login_name, 'r+')
    file_data = file.read()
    details = file_data.split('\n')
    current_balance = details[4]

    if float(withdraw_amount.get()) > float(current_balance):
        withdraw_notif.config(text='Insufficient funds*', font='georgia 10', fg='red', bg='gold')
        return
    updated_balance = current_balance
    updated_balance = float(updated_balance) - float(withdraw_amount.get())
    file_data = file_data.replace(current_balance, str(updated_balance))
    file.seek(0)
    file.truncate(0)
    file.write(file_data)
    file.close()
    current_balance_label.config(text='Current balance :$'+str(updated_balance), fg='green')
    withdraw_notif.config(text='Balance updated', fg='green', bg='gold')



def personal_details():
    # Variables
    file = open(login_name, 'r')
    file_data = file.read()
    user_details = file_data.split('\n')
    details_name = user_details[0]
    details_age = user_details[1]
    details_gender = user_details[2]
    details_balance = user_details[4]
    # Personal details screen
    personal_details_screen = Toplevel(win)
    personal_details_screen.title('Personal Details')
    personal_details_screen.configure(bg='gold')
    # Labels
    Label(personal_details_screen, text='Personal details', font='georgia 12 bold italic', bg='gold').grid(row=0, sticky=N, padx=5, pady=10)
    Label(personal_details_screen, text='NAME : '+details_name, font='georgia 10', bg='gold').grid(row=1, sticky='w', padx=5, pady=5)
    Label(personal_details_screen, text='AGE : ' + details_age, font='georgia 10', bg='gold').grid(row=2, sticky='w', padx=5, pady=5)
    Label(personal_details_screen, text='GENDER : ' + details_gender, font='georgia 10', bg='gold').grid(row=3, sticky='w', padx=5, pady=5)
    Label(personal_details_screen, text='AMOUNT :KSHS ' + details_balance, font='georgia 10', bg='gold').grid(row=4, sticky='w', padx=5, pady=5)


def login():
    # Variables
    global temp_login_name
    global temp_login_password
    global login_notif
    global login_screen
    temp_login_name = StringVar()
    temp_login_password = StringVar()
    # Login Screen
    login_screen = Toplevel(win)
    login_screen.configure(bg='gold')
    login_screen.title('Log In')
    # Labels
    Label(login_screen, text='Log In to your account', font='georgia 10 bold italic', bg='gold').grid(row=0, sticky=N, pady=10)
    Label(login_screen, text='Username:', font='georgia 10', bg='gold').grid(row=1, sticky='w', padx=5, pady=5)
    Label(login_screen, text='Password:', font='georgia 10', bg='gold').grid(row=2, sticky='w', padx=5, pady=5)
    login_notif = Label(login_screen, font='georgia 10', bg='gold')
    login_notif.grid(row=4, sticky=N, pady=5)
    # Entries
    Entry(login_screen, textvariable=temp_login_name, bd=5).grid(row=1, column=1, padx=5, pady=5)
    Entry(login_screen, textvariable=temp_login_password, show="*", bd=5).grid(row=2, column=1, padx=5, pady=5)
    # Button
    Button(login_screen, text='Log In', command=login_session,  bg='white', width=10, font='georgia 10 bold').grid(row=3, sticky='w', padx=5, pady=5)


    # Image import
img = ImageTk.PhotoImage(Image.open("logo.png"))


# Create a label
Label(win, text='SoCash \n Mobile Banking', font='georgia 15 bold italic', bg='gold').pack(anchor='center')
Label(win, text='The most secure online banking', font='georgia 12', bg='gold').pack(anchor='center')
Label(win, image=img, bg='gold').pack(fill=BOTH, expand=1, anchor='center')

# Buttons
Button(win, text='Register', font='georgia 10 bold', bg='white', width=20, command=register).pack(pady=10)
Button(win, text='Log-In', font='georgia 10 bold', bg='white', width=20, command=login).pack(pady=9)

win.mainloop()

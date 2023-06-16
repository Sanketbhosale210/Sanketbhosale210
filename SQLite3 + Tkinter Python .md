import tkinter as tk
from tkinter import ttk, messagebox
from tkinter import *
import sqlite3
import re

conn=sqlite3.connect("Demotable.db")
c=conn.cursor()

c.execute('''
    CREATE TABLE IF NOT EXISTS demot (name TEXT NOT NULL,phone INTEGER NOT NULL,email TEXT NOT NULL,Location TEXT NOT NULL)''')
        


def ADD():
    Person_name=e1.get()
    phone_no=e2.get()
    email_address=e3.get()
    Location_name=e4.get()
    pattern = r'^[\w\.-]+@[\w\.-]+\.\w+$'
    pattern1 = r'^[a-zA-Z\s]+$'
    
    
    
    if len(phone_no)==10 and phone_no.isdigit() and re.match(pattern,email_address) and re.match(pattern1,Location_name) and len(Person_name)<100:    
        try:
            c.execute('INSERT INTO demot (name,phone, email, Location) VALUES (?,?, ?,?)',
                       (Person_name,phone_no,email_address,Location_name))
            conn.commit()
            messagebox.showinfo("Demo","Inserted Successfully .....")
            e1.delete(0, END)
            e2.delete(0, END)
            e3.delete(0, END)
            e4.delete(0, END)
            e1.focus_set()
        except Exception as e:
            conn.rollback()
    else:
        messagebox.showinfo("Demo","Invalid Data ...")
        
def UPDATE():
    Person_name=e1.get()
    phone_no=e2.get()
    email_address=e3.get()
    Location_name=e4.get()
    c.execute("update demot set phone= ?,email=?,Location=? where name= ?",(phone_no,email_address,Location_name,Person_name))
    conn.commit()
    messagebox.showinfo("Demo","Updated Successfully ")
    e1.delete(0, END)
    e2.delete(0, END)
    e3.delete(0, END)
    e4.delete(0, END)
    e1.focus_set()


    
    
def DELETE():
    Person_name=e1.get()
    c.execute("delete from  demot where name=?",(Person_name,))
    conn.commit()
    messagebox.showinfo("Demo","Deleted Successfully ...")
        
        
def show():
    c.execute("Select * from demot order by Location")
    records=c.fetchall()
    print(records)
    
    for i,(name,phone, email, Location) in enumerate(records,start=1):
        listBox.insert("","end",values=(name,phone, email, Location))
        
 root = Tk()
root.geometry("800x600")

global e1
global e2
global e3
global e4

tk.Label(root, text=" Demo ",fg="black",font=(None,30)).place(x=400,y=5)

tk.Label(root,text="Name of person").place(x=10,y=10)
Label(root,text="Phone Number").place(x=10,y=40)
Label(root,text="Email ").place(x=10,y=70)
Label(root,text="Location").place(x=10,y=100)

e1=Entry(root)
e1.place(x=140,y=10)


e2=Entry(root)
e2.place(x=140,y=40)


e3=Entry(root)
e3.place(x=140,y=70)


e4=Entry(root)
e4.place(x=140,y=100)

Button(root,text="ADD",command=ADD,height=3,width=13).place(x=30,y=130)

Button(root,text="UPDATE",command=UPDATE,height=3,width=13).place(x=140,y=130)
Button(root,text="DELETE",command=DELETE,height=3,width=13).place(x=250,y=130)



cols=("Name ","Phone No","Email","Location")
listBox=ttk.Treeview(root, columns=cols, show="headings")

for col in cols:
    listBox.heading(col,text=col)
    listBox.grid(row=1,column=0,columnspan=2)
    listBox.place(x=10,y=200)
    
show()

listBox.bind('<Double-Button-1>',GetValue)

root.mainloop()




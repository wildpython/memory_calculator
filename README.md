# memory_calculator
Latest version of python Tkinter calculator using memory and history buttons
import tkinter
from tkinter import *
import math as m
import pyttsx3
from PIL import Image, ImageTk

#RESIZE BACKGROUND DISPLAY IMAGE
def display_image(image_path):
    image = PhotoImage(file=image_path)
    image_label = Label(root, image=image)
    image_label.image = image
    image_label.place(x=0, y=0, relwidth=2, relheight=2) #USE TO ADJUST BACKGROUND IMAGE

    #RESIZE IMAGE TO FIT SCREEN
    screen_width = root.winfo_screenwidth()
    screen_height = root.winfo_screenheight()
    image = image.subsample(image.width() //
                            screen_width, image.height() //
                            screen_height)

root = Tk()
root.geometry("700x700")#BIGGER SCREEN FOR EASY USEABILITY
#MAIN DIMENSIONS OF GRID
root.minsize(700, 700)
#MINIMUM DIMENSIONS
root.maxsize(700, 700)
#MAXIMUM DIMENSION
root.title("MY SMART CALCULATOR")
root.configure(background="White")

#DISPLAY MY BACKGROUND IMAGE USING FILE PATHS DOUBLE CLICK OPTION
display_image("/Users/gavinforbes/Desktop/Mixednumber.png") #CAN CHANGE IMAGE EASILY PNG IS GOOD

#ENTRY WIDGET
e = Entry(root, width=70, borderwidth=20, relief=RAISED, fg="yellow", bg="Black")
e.grid(row=2, column=0, columnspan=5, padx=10, pady=15)

#MEMORY STORAGE VARIABLE
memory = 0
history = []

def click(to_print):
    old = e.get()
    e.delete(0, END)
    e.insert(0, old + to_print)

def sc(event):
    key = event.widget
    text = key['text']
    no = e.get()
    result = ''
    if text == 'deg':
        result = str(m.degrees(float(no)))
    elif text == 'sin':
        result = str(m.sin(float(no)))
    elif text == 'cos':
        result = str(m.cos(float(no)))
    elif text == 'tan':
        result = str(m.tan(float(no)))
    elif text == 'lg':
        result = str(m.log10(float(no)))
    elif text == 'ln':
        result = str(m.log(float(no)))
    elif text == 'Sqrt':
        result = str(m.sqrt(float(no)))
    elif text == 'x!':
        result = str(m.factorial(int(no)))
    elif text == '1/x':
        result = str(1 / (float(no)))
    elif text == 'pi':
        result = str(m.pi if no == ""
else float(no) * m.pi)

    elif text == 'e':
        result = str(m.e if no == ""
else m.e ** float(no))


    e.delete(0, END)
    e.insert(0, result)

def clear():
    e.delete(0, END)

def bksps():
    current = e.get()
    length = len(current) - 1
    e.delete(length, END)

def evaluate():
    try:
        ans = e.get()
        ans = eval(ans)
        e.delete(0, END)
        e.insert(0, ans)
    except Exception as ex:
        e.delete(0, END)
        e.insert(0, "Error")
        print("Error:", ex) #BETTER ERROR HANDLING FOR DIVISION BY ZERO

#MEMORY FUNCTIONS
def memory_clear():#THE MC FUNCTION TO CLEAR ALL MEMORY AND RETURN TO ZERO
    global memory
    memory = 0
    history.append("MC: Cleared")

def memory_recall(): #THE MR FUNCTION TO RECALL EITHER A M+ OR M- FOR FURTHER MATHEMATICAL CALCULATIONS
    e.delete(0, END)
    e.insert(0, str(memory))

def memory_add(): #THE M+ FUNCTION TO ADD NEXT MEMERY INPUT
    global memory
    memory += float(e.get())
    history.append(f"M+: {e.get()}")

def memory_subtract(): #THE M- FUNCTION TO RECORD A NEGATIVE VALUE IN THE MEMORY
    global memory
    memory -= float(e.get())
    history.append(f"M-: {e.get()}")

def show_history(): #A NEW HISTORY SCREEN THAT WILL RECORD ALL MEMORY INPUTS FOR M+ M- MR AND MC FUNCTIONS
    history_window = Toplevel(root)
    history_window.title("Calculation History")

    history_window.geometry("400x300")
    history_frame = Frame(history_window)
    history_frame.pack(side=TOP, fill=BOTH, expand=True)

#CREATE A LISTBOX TO DISPLAY HISTORY USER INPUTS

    listbox = Listbox(history_frame, width=40, height=10)
    listbox.pack(side=LEFT, fill=BOTH, expand=True)

#CREATE A SCROLL BAR LINKED TO THE LISTBOX
    scroll = Scrollbar(history_frame, orient=VERTICAL)
    scroll.pack(side=RIGHT, fill=Y)

    listbox.config(yscrollcommand=scroll.set)

    scroll.config(command=listbox.yview)

#ADD HISTORY USER INPUTS TO LISTBOX
    for record in history: listbox.insert(END, record)

#HISTORY FUNCTIONS
mc = Button(root, text="MC", padx=32, pady=10, command=memory_clear, bg="Pink", fg="Blue")
mr = Button(root, text="MR", padx=32, pady=30, command=memory_recall, bg="Pink", fg="Blue")
mp = Button(root, text="M+", padx=32, pady=15, command=memory_add, bg="Pink", fg="Blue")
mm = Button(root, text="M-", padx=33, pady=10, command=memory_subtract, bg="Pink", fg="Blue")
h = Button(root, text="Hist", padx=30, pady=30, command=show_history, bg="Pink", fg="Blue")

#MEMORY AND HISTORY BUTTONS FOR SIZE AND POSITION ON GRID
mc.grid(row=8, column=3)
mr.grid(row=10, column=1)
mp.grid(row=8, column=2)
mm.grid(row=9, column=2)
h.grid(row=10, column=2)

#CALCULATION BUTTONS
lg = Button(root, text="lg", padx=37, pady=10, relief=RAISED, bg="Pink", fg="Blue")
lg.bind("<Button-1>", sc)

ln = Button(root, text="ln", padx=38, pady=10, relief=RAISED, bg="Pink", fg="Blue")
ln.bind("<Button-1>", sc)

par1st = Button(root, text="(", padx=40, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("("))
par2nd = Button(root, text=")", padx=40, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click(")"))
dot = Button(root, text=".", padx=40, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("."))
exp = Button(root, text="^", padx=38, pady=10, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("^"))
degb = Button(root, text="deg", padx=31, pady=10, relief=RAISED, bg="Pink", fg="Blue")
degb.bind("<Button-1>", sc)
sinb = Button(root, text="sin", padx=34, pady=10, relief=RAISED, bg="Pink", fg="Blue")
sinb.bind("<Button>", sc)
cosb = Button(root, text="cos", padx=33, pady=10, relief=RAISED, bg="Pink", fg="Blue")
cosb.bind("<Button>", sc)
tanb = Button(root, text="tan", padx=34, pady=10, relief=RAISED, bg="Pink", fg="Blue")
tanb.bind("<Button>", sc)

sqrtm = Button(root, text="Sqrt", padx=30, pady=10, relief=RAISED, bg="Pink", fg="Blue")
sqrtm.bind("<Button>", sc)

ac = Button(root, text="C", padx=37, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=clear)

bksp = Button(root, text="Bksp", padx=27, pady=15, relief=RAISED, bg="Pink", fg="Blue", command=bksps)
mod = Button(root, text="mod", padx=30, pady=10, relief=RAISED, bg="Pink", fg="Blue", command=evaluate)
div = Button(root, text="/", padx=40, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("/"))

fact = Button(root, text="x!", padx=38, pady=25, relief=RAISED, bg="Pink", fg="Blue")
fact.bind("<Button-1>", sc)

seven = Button(root, text="7", padx=40, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("7"))
eight = Button(root, text="8", padx=38, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("8"))
nine = Button(root, text="9", padx=38, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("9"))
mult = Button(root, text="*", padx=39, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("*"))

frac = Button(root, text="1/x", padx=35, pady=10, relief=RAISED, bg="Pink", fg="Blue")
frac.bind("<Button-1>", sc)
four = Button(root, text="4", padx=40, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("4"))
five = Button(root, text="5", padx=38, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("5"))
six = Button(root, text="6", padx=38, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("6"))
minus = Button(root, text="-", padx=38, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("-"))

pib = Button(root, text="pi", padx=36, pady=10, relief=RAISED, bg="Pink", fg="Blue")
pib.bind("<Button-1>", sc)
one = Button(root, text="1", padx=41, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("1"))
two = Button(root, text="2", padx=38, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("2"))
three = Button(root, text="3", padx=38, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("3"))
plus = Button(root, text="+", padx=37, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("+"))

e_b = Button(root, text="e", padx=40, pady=25, relief=RAISED, bg="Pink", fg="Blue")
e_b.bind("<Button-1>", sc)
zero = Button(root, text="0", padx=38, pady=25, relief=RAISED, bg="Pink", fg="Blue", command=lambda: click("0"))

equal = Button(root, text="=", padx=40, pady=30, bg="Pink", fg="Blue", relief=RAISED, command=evaluate)

#BUTTON PLACEMENT ON GRID
lg.grid(row=9, column=1)
ln.grid(row=9, column=0)
par1st.grid(row=5, column=0)
par2nd.grid(row=6, column=0)
dot.grid(row=6, column=1)

exp.grid(row=8, column=4)
degb.grid(row=7, column=1)
sinb.grid(row=8, column=0)
cosb.grid(row=7, column=0)
tanb.grid(row=8, column=1)

sqrtm.grid(row=7, column=2)
ac.grid(row=6, column=3)
bksp.grid(row=7, column=3)
mod.grid(row=9, column=3)
div.grid(row=3, column=4)

fact.grid(row=4, column=0)
seven.grid(row=3, column=1)
eight.grid(row=3, column=2)
nine.grid(row=3, column=3)
mult.grid(row=4, column=4)

frac.grid(row=9, column=4)
four.grid(row=4, column=1)
five.grid(row=4, column=2)
six.grid(row=4, column=3)
minus.grid(row=5, column=4)

pib.grid(row=7, column=4)
one.grid(row=5, column=1)
two.grid(row=5, column=2)
three.grid(row=5, column=3)
plus.grid(row=6, column=4)

e_b.grid(row=3, column=0)
zero.grid(row=6, column=2)
equal.grid(row=10, column=3)

#NOTES AND IMPROVEMENTS
#I HAVE TRIED MANY TIMES TO COLOUR THE BUTTONS BUT HAVEN'T BEEN SUCCESSFUL, MAYBE ANOTHER IDE
#WOULD LIKE TO ADD VOICE INTERACTION FOR LEARNING MATHEMATICS
#WOULD LIKE TO ADD GAME CHARACTERS TO THE UI FOR USERS TO INTERACT AND EARN POINTS

root.mainloop()

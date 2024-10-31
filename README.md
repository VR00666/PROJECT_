import mysql.connector as mysql
mycon=mysql.connect(host="localhost",user="root",passwd="MySQL",database="electronics")
mycur=mycon.cursor()
def Disp():
    
    mycur.execute("select * from table_1")
    goods = mycur.fetchall()
    print("PID   PNAME  COMPANY    WARRANTY    PRICE")
    for i in goods:
        print('\n',i[0],'\t',i[1],'\t',i[2],'\t','\t', i[3],'\t',i[4])


def delete():
    while True:
        c=input("enter the productID to be removed")
        mycur.execute("delete from table_1 where PID='{}'".format(c))
        mycon.commit()
        print(f"Deleted goods ID {c} from the database.")
        another = input("Do you want to delete another product? (yes/no): ")
        if another .lower()=='no':
            break
def update():
    while True:  
        pid = input("Enter the product ID to update: ")
        
        
        pname = input("Enter the new name of the product (leave blank to keep current): ")
        manufac = input("Enter the new manufacturer name (leave blank to keep current): ")
        warranty = input("Enter the new warranty details (leave blank to keep current): ")
        price = input("Enter the new price (leave blank to keep current): ")

        
        update_fields = []
        values = []
        
        if pname:
            update_fields.append("pname = %s")
            values.append(pname)
            
        if manufac:
            update_fields.append("manufacturer = %s")
            values.append(manufac)
            
        
        if warranty:
            update_fields.append("warranty = %s")
            values.append(warranty)
            
        if price:
            update_fields.append("price = %s")
            values.append(price)
         
        if update_fields:
            update_query = f"UPDATE table_1 SET {', '.join(update_fields)} WHERE pid = %s"
            values.append(pid)  
            
            mycur.execute(update_query, tuple(values))
            mycon.commit()
            print("Record updated successfully.")
        else:
            print("No fields to update.")

        another = input("Do you want to update another product? (yes/no): ")
        if another.lower() == 'no':
            break
def process_orders():
    try:
      
        mycur.execute("CREATE TABLE IF NOT EXISTS orders (SNO INT, PNAME VARCHAR(20), COMPANY VARCHAR(20), QUANTITY INT, PRICE INT)")
        mycur.execute("SELECT pname, company, price FROM table_1")
        d = mycur.fetchall()

        
        c = 1
        
        while True:
            print('\n_______ PRODUCTS _______\n')
            print("PNAME        COMPANY                 PRICE")
            for i in d:
                print(i[0], ' \t ', '\t', i[1], ' \t ', '\t', '₹', i[2])

            a1 = input('Enter the product: ')
            a2 = input('Enter the company: ')

            mycur.execute("SELECT pname, company, price FROM table_1 WHERE pname=%s AND company=%s", (a1, a2))
            q = mycur.fetchall()

            if not q:
                print("Invalid entry")
                continue

            pname = q[0][0]
            quan = int(input('Enter quantity: '))
            pri = q[0][2] * quan

            
            mycur.execute("INSERT INTO orders VALUES (%s, %s, %s, %s, %s)", (c, pname, a2, quan, pri))
            mycon.commit()

            c += 1

            ch = input('\nAdd more items? (yes/no): ')
            if ch.lower() == 'no':
                break

        print('\nBILL')
        mycur.execute('SELECT SNO, PNAME, COMPANY, QUANTITY, PRICE FROM orders')
        order = mycur.fetchall()

        
        print("S.NO   PNAME    company   QTY  PRICE")
        for i in order:
            print(i[0], '\t',i[1], "-", '\t',i[2], 'x' + str(i[3]), '->', '\t','₹', i[4])

        print('_______________')
        print('Total', '->', '₹', sum(i[4] for i in order))


        
        mycur.execute("DROP TABLE orders")
        mycon.commit()

        

    except mysql.Error as e:
        print(f"Database error: {e}")
    except ValueError:
        print("Invalid input. Please enter a valid quantity.")
    except Exception as e:
        print(f"An error occurred: {e}")

def add():
    while True:
        pid = input("Enter product ID: ")
        pname = input("Enter the name of the product: ")
        manufac = input("Enter name of the company: ")
        
        warranty = input("Enter warranty details: ")
        
        while True:
            price = input("Enter the price: ")
            try:
                price = float(price)
                break
            except ValueError:
                print("Please enter a valid number for price.")

        try:
            mycur.execute('''
                INSERT INTO table_1 (pid, pname, company, warranty, price) 
                VALUES (%s,%s, %s, %s, %s)
            ''', (pid, pname, manufac,warranty, price))
            
            mycon.commit()
            print(f"Added {pname} to the database.")
        except mysql.Error as err:
            print(f"Error: {err}")

        
        another = input("Do you want to add another product? (yes/no): ")
        if another .lower()=='no':
            break
print("_________________________________________________________________________"
      "\n \t𝐖𝐄𝐋𝐂𝐎𝐌𝐄 𝐓𝐎 𝐄𝐋𝐄𝐂𝐓𝐑𝐎𝐍𝐈𝐂 𝐆𝐎𝐎𝐃𝐒 𝐌𝐀𝐍𝐀𝐆𝐄𝐌𝐄𝐍𝐓 𝐒𝐘𝐒𝐓𝐄𝐌"
      "\n_________________________________________________________________________")
while True:
    try:
        print('''\n PRESS 1 : TO VIEW PRODUCTS\n
PRESS 2 : TO ADD PRODUCTS \n
PRESS 3 : TO UPDATE DATABASE\n
PRESS 4 : TO DELETE PRODUCTS\n
PRESS 5 : TO PLACE ORDERS\n
PRESS 6 : TO EXIT\n''')
        
        op = int(input("PLEASE ENTER YOUR CHOICE: "))  
        
        if op == 1:
            Disp()
        elif op == 2:
            add()
        elif op == 3:
            update()
        elif op == 4:
            delete()
        elif op == 5:
            process_orders()
        elif op == 6:
            
            print("____________________________"
      "\n \t 𝗧𝗛𝗔𝗡𝗞 𝗬𝗢𝗨"
      "\n_____________________________")
            break
        else:
            print('Invalid option. Please try again.')
    except ValueError:
        print("Invalid input. Please enter a number between 1 and 6.")






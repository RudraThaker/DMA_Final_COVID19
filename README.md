# DMA_Final_COVID19
COVID19 DBMS for Universities created in MySQL and python application to send notifications

** Establish DB connection

    def createConnection(host_name, user_name, user_password, db_name):
        pw = ''
        connection = None
        try:
            connection = mysql.connector.connect(
                host=host_name,
                user=user_name,
                passwd=user_password,
                database=db_name
            )
            print("MySQL Database connection successful")
        except Error as err:
            print(f"Error: '{err}'")

        return connection
        
This function accepts your Database hostname, username, password and schema name.

    connection = createConnection("localhost", "root", "" ,'COVIIDdb')

I have left the password string empty.

**Read and execute queries
Following methods will be used to read and execute SQL queries upon successfully connecting to the database schema.
     
     def executeQuery(connection, query):
        cursor = connection.cursor()
        try:
            cursor.execute(query)
            connection.commit()
            print("Query successful")
        except Error as err:
            print(f"Error: '{err}'")

    def executeInserts(connection, query, values):
        cursor = connection.cursor()
        try:
            cursor.execute(query, values)
            connection.commit()
            print("Query successful")
        except Error as err:
            print(f"Error: '{err}'")



    def readQuery(connection, query):
        cursor = connection.cursor()
        result = None
        try:
            cursor.execute(query)
            result = cursor.fetchall()
            return result
        except Error as err:
            print(f"Error: '{err}'")
            
 **Email Notifications
 The soul purpose of this application is to notify students on their COVID test results and Vaccination status.
 Connection to the gmail derver is established through the following python library:
    
    import smtplib
 
 We first create a Gmail account and enable app password. We use this app password to connect to the Gmail server and send emails whenever the function is called.
    
    def emailNotification(subject, body, to):
      msg= EmailMessage()
      msg.set_content(body)
      msg['subject']= subject
      msg['to']= to

      user= 'dmacovid@gmail.com'
      msg['from']= user
      password= 'otakkqloodnmtfht'

      server= smtplib.SMTP('smtp.gmail.com', 587)
      server.starttls()
      server.login(user, password)
      server.send_message(msg)
      print("Email successfully sent")
      server.quit()
      
Let's test this with a test email

    subject= 'please get tested'
    to= 'rudrathaker26@gmail.com'
    body= 'Hello, test msg'
    emailNotification(subject, body, to)
    
    Email successfully sent

**Here is one of the Notification criteria implementation

    query_emails= '''SELECT p.emailID FROM person p
      JOIN testAppointment ta ON (p.NUID = ta.NUID)
        JOIN testEvent te ON (ta.appointmentID = te.appointmentID)
        JOIN report r ON (te.eventID = r.sampleID) where r.result = 1;'''

    subject1= 'COVID POSITIVE'

    body1= 'You have been tested Positive for Covid-19, Please report to th University for quarantine advices.'

    emails = readQuery(connection, query_emails)
    for email in emails:
    #       print(email)
         emailNotification(subject1, body1, to = email)
    print("Emails successfullt sent")
    
    Email successfully sent
    Email successfully sent
    Email successfully sent
    Email successfully sent
    Email successfully sent
    Email successfully sent
    Email successfully sent
    Email successfully sent
    Email successfully sent
    Emails successfullt sent
    
 ![image](https://user-images.githubusercontent.com/75456477/117183689-d504c200-ada5-11eb-9999-28417d280d9a.png)

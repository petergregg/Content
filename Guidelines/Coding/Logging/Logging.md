

# Why
- Speeds up feedback loop
- Transparency of processes - use INFO to record the steps and process a system has taken
- Faster fixes and troubleshooting - use Error to collect detailed crash analytics 

# What
- Log communications between components
    - Microservices
- Log communications with external API's
- Add valuable meta data
    - User Id
    - Transaction Id
    - Location
    - Cost
    - CPU
    - Threads
    - Memory
- Remember you are not the only one reading these logs
- Combine textual and metric fields

# Log serverity

### Debug/verbose
Background processes that are happening 

'Number of notifications in the notification queue = 2481'
### Info - 
'Announcment completed sucessfully'
### Warning 
- Doesn't effect the flow of the software but could affect the customer experience 

'Recieved illegal character for username - "Jame$', ignoring char'
### Error
- System failed 
- Someone failed to log in > 1000

'Failed to execute query for user ID 788977887'

### Critical/Fatal 
- Use very carefully
- Stop everything and fix

'No connection to database, failed to log user in'


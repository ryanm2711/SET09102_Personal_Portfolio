# Selected Code
```
public void Register(string username, string password){ 
  var salt = CreateSalt(); 
  var hashedPassword = HashPassword(password, salt); 
  SaveToDatabase(username, hashedPassword, salt); 
} 

public bool Login(string username, string password){ 
  var user = GetUserFromDatabase(username); 
  var hashedPassword = HashPassword(password, user.Salt); 

  if(user.HashedPassword == hashedPassword){ 
    return true; 
  } 
  return false; 
}
```
This code violates the KISS (Keep It Simple, Stupid) principle. This is violated at the very end where it's returning a true or false value - this solution isn't inherintly wrong, but it is bad practice as it's way more convoluted than it needs to be. Instead those 4 lines of code can be boiled down to one line and make it less error prone too.

Another violation is the lack of comments in the code, leaving the understanding up to the reader to figure out. This isn't good for two reasons: 1. Other developers did not write the code, so they have to go through the hurdle of attempting to understand it before doing any work, which eats up time. And 2. The writer of this code may come back to it years later and not know what is going on due to not remembering their logic behind it at the time.

And lastly, there is no exception handling for if the account that is being attempted to login to actually exists. This is a fairly simple thing to implement but the lack of implementation can cause the whole program to breakdown.

# My Solution
```
// Purpose:
// Register an account and save it to the database with the information of a username & password
public void Register(string username, string password){ 
  // Create a hashed password by generating a seed to hash from. This is what we call salt
  // Then we store the hashed password into a separate variable, not overwriting the input data
  var salt = CreateSalt(); 
  var hashedPassword = HashPassword(password, salt); 

  // Now lets call SaveToDatabase and feed in our seed, username & hashed password
  SaveToDatabase(username, hashedPassword, salt); 
} 

// Purpose:
// Login to an account that is already inside the database.
// Returns: boolean depending on the success of the login
public bool Login(string username, string password){ 
  // Get the selected user account from database with the inputted username
  // Then check to see if that account exists, if not return false saying the login was unsuccessful.
  var user = GetUserFromDatabase(username);
  if (user == null)
      return false;

  // Now lets get the hashed password again and check to see if it is the same as the stored hashed password from the database
  // Return a boolean value based on if the password is the same or not, stating if the login was successful or not.
  var hashedPassword = HashPassword(password, user.Salt); 
  return user.HashedPassword == hashedPassword
}
```
That simple change made the entirety of that method a whole of a lot easier to read, and code that's easier to read makes it easier to maintain and understand for yourself and the rest of the team working on the project. 

Here are the changes:
- Instead of the if statements to return if the login was a success, I made it a single line by returning the comparasion that was previously done in the if statement.
- Now there is exception handling for no account existing for the user that's trying to log into. If the account is null, we simply return false - which means it exits the login method and states that the login failed/was false.
- All the code now is commented, giving a better insight into what each line of code is actually doing.

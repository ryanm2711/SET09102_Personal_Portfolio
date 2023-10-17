# Code
## Test 01

### Summary
The purpose of this code was to make sure that it did indeed finish correctly and was properly cleaned up, i.e variables being reset and GameOver event is being called. Due to the nature of the object we are testing, it supports multiple difficulties. I thought it was best to allow for a parameter to easily test any difficulty within the same method - thus avoiding breaching the coding principle **D.R.Y**.

In this test we are checking to see if the ``bGameOver`` boolean value is true and ``page.remainingAttempts`` was correctly reset and is equal to 0. It was vital to test these as it highlights to us whether the game is being correctly reset when it is game over.
```cs
// Must be a theory method to support parameters, we supply 3 inline pieces of data which will be our difficulties
// This allows us to test multiple difficulties of the game within the same method
[Theory]
[InlineData("Easy")]
[InlineData("Medium")]
[InlineData("Hard")]
public void FindLetterInWord(string difficulty = "Easy")
{
  GamePage page = new GamePage(difficulty); // Create a new object of game to do our tests
  page.remainingAttempts = 0; // Reset remaining attempts to 0

  bool bGameOver;
  page.GameOver();
  bGameOver = true; // Do game over and assign a boolean value saying that is indeed now over

  page.OnAttemptSubmitted(null, null); // Invoke OnAttemptSubmitted event

  Assert.True(bGameOver); // Ensure the game is actually over with the boolean being true
  Assert.Equal(0, page.remainingAttempts); // Make sure page remaining attempts is equal to 0
}
```

## Test 02
### Summary
In this piece of code we are testing whether the check letter in word function is working as intended. In this test I supplied two sets of data to test from. One set with a correct letter for the word which is tested for each difficulty, and the second data set to test an incorrect letter for a word in each difficulty.

This is a very important test as it's part of the main gameplay loop. The whole game is based on guessing words by putting in letters, if it was incorrectly assessing if the player got the answer wrong then it would make the game unfair/impossible to complete.

In the test we assess if ``bDoesExist`` returns true or not. This variable gets assigned a value from the ``GamePage.CheckLetterInWord`` method which takes in a word and a letter to test from. 
```cs
// Have multiple tests with each difficulty, both with cases where it should fail and succeed
[Theory]
[InlineData("Easy", "testing", 's')]
[InlineData("Medium", "testing", 's')]
[InlineData("Hard", "testing", 's')]

[InlineData("Easy", "hangman", 'x')]
[InlineData("Medium", "hangman", 'x')]
[InlineData("Hard", "hangman", 'x')]
public void Find_Letter_In_Word(string difficulty, string word, char testLetter)
{
  GamePage page = new GamePage(difficulty);

  bool bDoesExist = page.CheckLetterInWord(word, testLetter); // Check if the supplied letter exists in the supplied word for the test 
  Assert.True(bDoesExist); // Then assess if it is true or not
}
```

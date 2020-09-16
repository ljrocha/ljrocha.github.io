---
layout: post
title: "Hello, Swift Algorithm!"
date: 2020-09-15 7:00:00 -0800
tags: swift algorithm
---

*Hello, world!* I have recently been preparing for iOS interviews, and as part of my preparation, I've been reviewing data structures and algorithms using Swift. Having recently solved an interesting algorithm problem, I've decided to share my solution in my first blog post.

I will break this post down into five main sections: the problem, my assumptions, the solution, testing, and a conclusion.

I hope you enjoy reading about how I approached this problem and learning about my algorithm solution!

## The Problem
> Write a function that takes a string, that will contain single digit numbers,
 letters, and question marks, and check if there are exactly 3 question marks between every
 pair of two numbers that add up to 10. If so, then your program should return true,
 otherwise it should return false. If there aren't any two numbers that add up to 10 in the string,
 then your program should return false as well

Having read the problem multiple times, the first thing I did was break down the problem to fully understand it and solve it correctly.

Here's what we know:
* We need to write a function that takes in a string and returns a Boolean value
* The input string will contain single digit numbers (0-9), letters (a-z, A-Z), and question marks (?)
* We need to check *every* pair of numbers that add up to 10, and make sure there are exactly 3 question marks between them
  * If that's the case, we return `true`
  * If that's not the case, we return `false`
* In the case that the input string does not contain any two numbers that add up to 10, we return `false`

## Assumptions
After thoroughly reading the problem, I needed to make some assumptions before working on a solution.

My assumptions were as follows:
* The input string will not be empty *(although, we can easily perform a check to make sure the string is not empty using a `guard` statement)*
* The input string might contain duplicate numbers, which leads me to my next assumption
* We must check **every** pair of numbers that add up to 10
  * For example, in the string "3H?elloWo3???ld7" we can see that the number 7 matches with two 3's, so this string has two pairs of numbers that add up to 10. However, only one of the two pairs contains exactly 3 question marks between its two numbers. The other pair contains 4 question marks between its two numbers. As a result, this input string should return `false`.

## Solution
Having an understanding of the problem and my assumptions in place, I needed to come up with a solution.

#### The Brute-Force Way
My first thought to a solution was to iterate through the characters in the string looking for digits. If a number was found, I would then proceed to look for its complement in the remaining characters of the string while also keeping a count of the question marks I encountered along the way. If the complement was found, I would check the question mark count and ensure it was exactly 3. This brute-force solution works, but it has a runtime of *O(n<sup>2</sup>)*. Surely, there had to be a more optimal solution.

#### A More Optimal Solution
After coming up with a brute-force solution, I began thinking about what an optimal solution would look like. I knew that every character in the string had to be examined because the digits or question marks could be at any position in the string. This meant that the best conceivable runtime was *O(n)*. However, I also knew that I needed to account for duplicate numbers and that this could potentially impact the runtime.

The algorithm I came up with works as follows:
* I use a flag to indicate that at least one pair of numbers that add up to 10 was found. Initially, this is set to `false`
* I then iterate through the string looking for digits and question marks
  * The fun begins when a digit is found
    * If this is the first digit I've seen:
      * I reset the current number of question marks to 0 *(any previously seen question marks can be safely ignored, as those won't be between any two numbers)*
      * I store the digit along with the current question mark count of 0
      * I then continue with the next iteration of the loop
    * If this is not the first digit I've encountered, I check if the complement has been seen. If the complement has been seen:
      * I set the flag indicating that at least one pair of numbers that add up to 10 has been found
      * I find the difference between the current number of question marks I have seen so far and the number of question marks I had seen at the point I saw the complement. The difference must be exactly 3, or else I return `false` and exit out of the function *(the complement might have been seen multiple times before, so I check the difference for each of those times)*
    * I then store the digit I just found along with the current number of question marks up to this point
  * Every time I encounter a question mark, I increment the current question mark count
* If we reach the end of the function, I return the value of the flag that was used to indicate if at least one pair of numbers that add up to 10 was found *(we know that at this point, either all pairs of numbers that add up to 10 have exactly 3 question marks between them or no such pair exists)*

Here's the code:

```swift
func isValid(_ string: String) -> Bool {
    // Store the digits in the string along with the # of
    // question marks we have seen up to the point when the
    // digit is encountered
    var previousDigits = [Int: Set<Int>]()

    // Running count for the # of question marks in the string
    var currentQuestionMarkCount = 0

    // At least one pair of numbers that add up to 10 was found
    var foundPair = false

    for character in string {
        if let digit = Int(String(character)) {

            // Check if this is the first digit we've seen
            guard !previousDigits.isEmpty else {
                currentQuestionMarkCount = 0
                previousDigits[digit, default: []].insert(0)
                continue
            }

            // Check if we've seen its complement
            let complement = 10 - digit
            if let previousQuestionMarkCounts = previousDigits[complement] {
                foundPair = true
                for previousQuestionMarkCount in previousQuestionMarkCounts {
                    guard currentQuestionMarkCount - previousQuestionMarkCount == 3 else { return false }
                }
            }

            // Store the digit along with the current question mark count
            previousDigits[digit, default: []].insert(currentQuestionMarkCount)
        } else if character == "?" {
            currentQuestionMarkCount += 1
        }
    }

    return foundPair
}
```

Some things to note:
* I needed to store each digit I encountered along with the current question mark count up to that point
  * This meant that if a duplicate number was found with a different number of question marks up to that point, I had to either: store the digit again or store it once and keep track of the multiple question mark counts
* I decided that a dictionary was a great way to store the digits and its corresponding question mark counts
  * Dictionary keys are required to be `Hashable`, so I wouldn't be storing duplicate numbers more than once
  * Also, checking if a particular key exists would be done in constant time
  * The dictionary values are of type `Set<Int>`, so I'm not storing duplicate question mark counts for each digit

## Testing
After coding up my algorithm solution, I needed to perform some tests. The following code shows some of the tests I performed:

```swift
print(isValid("5H?ello2?Wor?ld5?8"))
// Prints 'true'

print(isValid("7H?e?llo?3Wo?rld3"))
// Prints 'false'

print(isValid("?h3llo1??w?9rld7?ea?s?y31"))
// Prints 'true'
```

These tests and others I performed seem to indicate that my solution is correct (for my test cases, at least). 😅

## Conclusion
Although this was by no means a difficult algorithm problem to solve, I enjoyed solving it. So much so that I wanted to share how I approached this problem. I hope you enjoyed learning about my solution!

I love learning about Swift and iOS development, so please feel free to reach out if you have any suggestions for improving my solution. Also, feel free to point out any errors I might have missed as I'm always looking to improve as a software engineer!

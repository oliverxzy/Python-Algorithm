### Sliding Window Notes
**Use case**: dealing with an array (or LinkedList), asked find or calculate something among all the subarrays (or sublists) of a given size.

For most of sliding window Case, We need:
- window_end (looping)
- window_start (shrink as not meet question requirement)
- hash_table (if need calculate frequency related)
and whole structure should be:
```python
def func(string_s):
	window_start = 0

	for window_end in range(len(string_s)):
		# 根据题目需要 记录向右移动后新增字符
		# 如果与frequency有关，则需要使用hash table记录frequncy
		...
		
		if (window need shrink):
			# 根据题目需要 移除字符
			...
			window_start += 1
			
	return target_result
		
```

### Maximum Sum Subarray of Size K (easy)
Given an array of positive numbers and a positive number ‘k,’ find the **maximum sum of any contiguous subarray of size ‘k’**.

**Solving Idea**
- For finding the maximum sum of K, we are using sliding method.
- assign a dynamic sum that in each loop, compare with the temp largest
	- if not > largest, then add WindowEnd to Sum, substract WindowStart from Sum
	- If > largest, temp sum = current sum, then move to next
**Solution**
```python
def max_sub_array_of_size_k(k, arr):
# TODO: Write your code here
	max_sum = 0
	window_sum = 0
	window_start = 0
	for window_end in range(len(arr)):
		window_sum += arr[window_end]
		if window_end >= k - 1:
			max_sum = max(max_sum,window_sum)
			window_sum -= arr[window_start]
			window_start += 1
	return max_sum
```

### Smallest Subarray With a Greater Sum(easy)
Given an array of positive integers and a number ‘S,’ find the length of the **smallest** contiguous subarray whose sum is **greater than or equal to ‘S’**. Return 0 if no such subarray exists.

**Solving Idea**
- it is pretty similar to what we done before. but since it is not fix window, we want first set a condition that when sum meet requirement:
	- keep shrink window untill window's sum is smaller than requirement
	- record min_length and then move to next
**Solution**
```python
import math
def smallest_subarray_sum(s, arr):
	min_length = math.inf
	# TODO: Write your code here
	window_start = 0
	window_sum = 0
	for window_end in range(len(arr)):
		window_sum += arr[window_end]
		while window_sum >= s:
			min_length = min(min_length,window_end - window_start + 1)
			window_sum -= arr[window_start]
			window_start += 1
	return min_length
```

### Longest Substring with maximum K Distinct Characters (medium)
Given a string, find the length of the longest substring in it with no more than K distinct characters.
```
Input: String="araaci", K=2
Output: 4
Explanation: The longest substring with no more than '2' distinct characters is "araa".
```

**Solving Idea**
- Using hashtable to store characters frequency
- add next one,  if len(dict) > k, then remove window_start

**Solution**
```python
def longest_substring_with_k_distinct(str1, k):
  window_start = 0
  max_length = 0
  char_frequency = {}

  # in the following loop we'll try to extend the range [window_start, window_end]
  for window_end in range(len(str1)):
    right_char = str1[window_end]
    if right_char not in char_frequency:
      char_frequency[right_char] = 0
    char_frequency[right_char] += 1

    # shrink the sliding window, until we are left with 'k' distinct characters in the char_frequency
    while len(char_frequency) > k:
      left_char = str1[window_start]
      char_frequency[left_char] -= 1
      if char_frequency[left_char] == 0:
        del char_frequency[left_char]
      window_start += 1  # shrink the window
    # remember the maximum length so far
    max_length = max(max_length, window_end-window_start + 1)
  return max_length
```

### Fruits into Baskets (medium)
You are visiting a farm to collect fruits. The farm has a single row of fruit trees. You will be given two baskets, and your goal is to pick as many fruits as possible to be placed in the given baskets.
You will be given an array of characters where each character represents a fruit tree. The farm has following restrictions:
1.  Each basket can have only one type of fruit. There is no limit to how many fruit a basket can hold.
2.  You can start with any tree, but you can’t skip a tree once you have started.
3.  You will pick exactly one fruit from every tree until you cannot, i.e., you will stop when you have to pick from a third fruit type.
Write a function to return the maximum number of fruits in both baskets.

**Solving Idea**
This one transform into [[Sliding Window#Longest Substring with maximum K Distinct Characters medium]] with K = 2.
- create a hashtable to store string and occur times
- add next one, if len(arr) > 2, then substract window_start

**Solution**
```python
def fruits_into_baskets(fruits):
  hashtable = {}
  max_len = 0
  window_start = 0
  for window_end in range(len(fruits)):
    right_char = fruits[window_end]
    if right_char not in hashtable:
      hashtable[right_char] = 0
      hashtable[right_char] += 1
    while len(hashtable) > 2:
      left_char = fruits[window_start]
      hashtable[left_char] -= 1
      if hashtable[left_char] == 0:
        del hashtable[left_char]
      window_start += 1
    max_len = max(max_len, window_end - window_start + 1)
  # TODO: Write your code here
  return max_len
```

### Longest Substring with Distinct Characters (hard)
Given a string, find the **length of the longest substring**, which has all **distinct characters**.
```
Input: String="aabccbb"
Output: 3
Explanation: The longest substring with distinct characters is "abc".
```

**Solving Idea**
- we will have:
	- window_start (+=1 when meet requirement)
	- window_end (moving in loop)
	- hash_table (**store existed characters and its position index**)
- when current window_end exist in hash_table, move left_window to window_end + 1
- calculate max(max_len, window_end - window_start + 1)
	- when window_end < window_start, max always be previous max

**Solution**
```python
def non_repeat_substring(str):
  window_start = 0
  max_len = 0
  has = {}
  for window_end in range(len(str)):
    right_char = str[window_end]

    if right_char in has:
      window_start = max(window_start, has[right_char] + 1)
    has[right_char] = window_end
    max_len = max(max_len, window_end - window_start + 1)
  # TODO: Write your code here
  return max_len
```

### Longest Substring with Same Letters after Replacement (hard)
Given a string with lowercase letters only, if you are allowed to **replace no more than `k` letters** with any letter, find the **length of the longest substring having the same letters** after replacement.
```
Input: String="aabccbb", k=2
Output: 5
Explanation: Replace the two 'c' with 'b' to have the longest repeating substring "bbbbb".
```

**Solving Idea**
- we will have:
	- window_start (+=1 when meet requirement)
	- window_end (moving in loop)
	- max_length
	- hash_table (**store characters in the window and their frequency**)
	- maxRepeatLetterCount (count of the maximum repeating letter in any window)
- Since we are keep recording maxRepeatLetterCount, to find longest required substring is using maxRepeatLetterCount + K
> For example: a[abcc]bb. in the window we meet the requirement that distinct_letter <= k, then its max_repeat = window_end - window_start + 1 + k

- For loop (window_end move to next):
	1. record frequency of the character at window_end
	2. tracking `maxRepeatLetterCount` and compare for new added character at window_end
	3. determine if requirement_K meet:
		1. if not meet: 
			1. remove left side letter from hash_table
			2. move window_start to next
	4. record and compare max_length
- return max_length

**Solution**
```python
def length_of_longest_substring(str1, k):
  window_start, max_length, maxReCount = 0, 0, 0
  hash_table = {}
  for window_end in range(len(str1)):
    char = str1[window_end]
    if char not in hash_table:
      hash_table[char] = 0
    hash_table[char] += 1

    maxReCount = max(maxReCount,hash_table[char])

    if window_end - window_start + 1 - maxReCount > k:
      left_char = str1[window_start]
      hash_table[left_char] -= 1
      window_start += 1
    
    max_length = max(max_length,window_end - window_start + 1)
  # TODO: Write your code here
  return max_length
```

### Longest Subarray with Ones after Replacement (hard)
Given an array containing 0s and 1s, if you are allowed to **replace no more than ‘k’ 0s with 1s**, find the length of the **longest contiguous subarray having all 1s**.
Example
```
Input: Array=[0, 1, 1, 0, 0, 0, 1, 1, 0, 1, 1], k=2
Output: 6
Explanation: Replace the '0' at index 5 and 8 to have the longest contiguous subarray of 1s having length 6.
```

**Solving Idea**
- we will have:
	- window_start (+=1 when meet requirement)
	- window_end (moving in loop)
	- max_length
	- zero_occur_time
- For loop (window_end move to next):
	- if Array[window_end] = 0:
		- zero_occur_time += 1
	- if zero_occur_time > k:
		- window_start += 1
		- zero_occur_time -= 1
	- max_length = max(max_length, window_end - window_start + 1)

**Solution**
```python
def length_of_longest_substring(arr, k):
  window_start, max_length, zero_occur = 0, 0, 0
  
  for window_end in range(len(arr)):
    if arr[window_end] == 0:
      zero_occur += 1
    
    if zero_occur > k:
      left_int = arr[window_start]
      if left_int == 0:
        zero_occur -= 1
      window_start += 1
    max_length = max(max_length, window_end - window_start + 1)
  # TODO: Write your code here
  return max_length
```

### Permutation in a String (hard) - Problem Challenge 1
Given a string and a pattern, find out if the **string contains any permutation of the pattern**.
**Permutation** is defined as the re-arranging of the characters of the string. For example, “abc” has the following six permutations:
1.  abc
2.  acb
3.  bac
4.  bca
5.  cab
6.  cba
If a string has ‘n’ distinct characters, it will have n!n! permutations.
```
Input: String="oidbcaf", Pattern="abc"
Output: true
Explanation: The string contains "bca" which is a permutation of the given pattern.
```

**Solving Idea**
For pattern, we are using hash_table to record each char and it's occur times
For finding result, we need variables:
- window_start
- window_end
- 
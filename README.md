# sum-prod-in-python
# code challenge for online course MITx: 6.00.2x Introduction to Computational Thinking and Data Science, Final Exam

# Code Challenge:
# Given a list of integers and a target integer, return a numpy array of 1 or 0 values, such that the sumproduct of the list and corresponding array returns the target integer.  In the case of ties, return the numpy array with the fewest non-zero elements.

# For example:
# If choices = [1,2,2,3] and total = 4 you should return either [0 1 1 0] or [1 0 0 1]
# If choices = [1,1,3,5,3] and total = 5 you should return [0 0 0 1 0]
# If choices = [1,1,1,9] and total = 4 you should return [1 1 1 0]


import numpy as np

# this is the master function, that takes a list 'choices' and integer 'total' and returns a numpy array of 0 and 1 values such that the sumproduct of 'choices' and 'find_combination' results in 'total'

def find_combination(choices, total):
    if len(choices) == 0:
        return np.array([])
    return np.array(find_best_result(choices, total))

# ***** helper functions *****

def get_series(r):
    # given a list, return a list of lists,
    # each item in the list of lists is a sequence of 1s and 0s, represented in binary
    # that account for every combination of 1s and 0s
    # in the length of the array
    
    result = []
    max_string = '1' * len(r)
    max_binary = int(max_string, 2) + 1
    for x in range(max_binary):
        sub = []
        binary_equivalent = bin(x)[2:]
        leading_zeros = '0' * (len(r) - len(binary_equivalent))
        t = leading_zeros + binary_equivalent
        for val in t:
            sub.append(int(val))
        result.append(sub)
    return result

def sumprod(choices, factor):
    # returns the sum product of choices and test_list
    # assumes choices and factor are lists of equal length
    result = 0
    for n in range(len(choices)):
        result += choices[n] * factor[n]
    return result

def get_possible_winners(choices, total):
    # returns a list of lists
    # such that each list, when applied, the sumproduct of the
    # choices results in total
    # if no such total exists, then it returns the closest without exceeding total
    
    result = []
    series = get_series(choices)
    
    alt_result = []
    default = [0] * len(choices)
    next_best = (default, 0)

    for s in series:
        val = sumprod(choices, s)
        if val == total:
            result.append(s)
        else:
            alt_result.append((s, val))
    if len(result) == 0:
        for i in alt_result:
            if i[1] > next_best[1] and i[1] < total:
                next_best = (i[0], i[1])
        result.append(next_best[0])
    return result

def find_best_result(choices, total):
    result = None
    best_val = None
    for n in get_possible_winners(choices, total):
        total = 0
        for c in n:
            total += c
        if best_val is None:
            best_val = total
            result = n
        if total < best_val:
            best_val = total
            result = n
    return result

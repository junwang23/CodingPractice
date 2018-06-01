# All O\`one Data Structure

LintCode 1245

LeetCode 432

## Problem

#### Description

Implement a data structure supporting the following operations:

* Inc\(Key\) - Inserts a new key with value 1. Or increments an existing key by 1. Key is guaranteed to be a non-empty string.
* Dec\(Key\) - If Key's value is 1, remove it from the data structure. Otherwise decrements an existing key by 1. If the key does not exist, this function does nothing. Key is guaranteed to be a non-empty string.
* GetMaxKey\(\) - Returns one of the keys with maximal value. If no element exists, return an empty string "".
* GetMinKey\(\) - Returns one of the keys with minimal value. If no element exists, return an empty string "".

#### Challenge

Perform all these in O\(1\) time complexity.

## Solution

`Inc(Key)` and `Dec(Key)` requires a hash table for O\(1\) time, the difficult part is to keep tracking the keys with max and min values in O\(1\) time. This can be achieved by maintaining a double-linked list, where each node records all the keys with the same value and can be visited with a &lt;value-node&gt; hash table. This is similar to a bucket sort. 

When the value of a key is increased or decreased, we remove the key from its current node and add it to the new node. If the new node regarding the new value does not exist or the old node needs to be removed, the insertion/deletion can be done in O\(1\) as this is a double-linked list.

We also keep tracking the head and tail of the double link list, and keep the list sorted. Then we can achieve `GetMaxKey()` and `GetMinKey()` in O\(1\) time by just visiting the head or the tail.

#### Code

```java
class AllOne {
    
    class Node {
        int value;
        Set<String> keys;
        Node prev, next;
        Node(int v) {
            value = v;
            keys = new HashSet<>();
        }
    }
    
    Map<String, Integer> keys;
    Map<Integer, Node> buckets;
    Node head, tail;

    /** Initialize your data structure here. */
    public AllOne() {
        keys = new HashMap<>();
        buckets = new HashMap<>();
        head = new Node(Integer.MIN_VALUE);
        tail = new Node(Integer.MAX_VALUE);
        head.next = tail;
        tail.prev = head;
    }
    
    /** Inserts a new key <Key> with value 1. Or increments an existing key by 1. */
    public void inc(String key) {
        int val = keys.getOrDefault(key, 0);
        keys.put(key, val + 1);
        Node curr = val == 0 ? head : buckets.get(val);
        if (curr.next.value > val + 1) {
            Node next = new Node(val + 1);
            insertBucketAfter(curr, next);
        }
        curr.next.keys.add(key);
        if (curr != head) {
            curr.keys.remove(key);
            if (curr.keys.size() == 0)
                removeBucket(curr);
        }
    }
    
    /** Decrements an existing key by 1. If Key's value is 1, remove it from the data structure. */
    public void dec(String key) {
        if (keys.containsKey(key)) {
            int val = keys.get(key);
            Node curr = buckets.get(val);
            curr.keys.remove(key);
            if (val == 1) {
                keys.remove(key);
            } else {
                keys.put(key, val - 1);
                if (curr.prev.value < val - 1) {
                    Node prev = new Node(val - 1);
                    insertBucketAfter(curr.prev, prev);
                } 
                curr.prev.keys.add(key);
            }
            if (curr.keys.size() == 0)
                removeBucket(curr);
        }
    }
    
    /** Returns one of the keys with maximal value. */
    public String getMaxKey() {
        if (tail.prev == head) return "";
        return tail.prev.keys.iterator().next();
    }
    
    /** Returns one of the keys with Minimal value. */
    public String getMinKey() {
        if (head.next == tail) return "";
        return head.next.keys.iterator().next();
    }
    
    void insertBucketAfter(Node prev, Node curr) {
        curr.next = prev.next;
        curr.prev = prev;
        prev.next = curr;
        curr.next.prev = curr;
        buckets.put(curr.value, curr);
    }
    
    void removeBucket(Node curr) {
        curr.prev.next = curr.next;
        curr.next.prev = curr.prev;
        buckets.remove(curr.value);
    }
}

/**
 * Your AllOne object will be instantiated and called as such:
 * AllOne obj = new AllOne();
 * obj.inc(key);
 * obj.dec(key);
 * String param_3 = obj.getMaxKey();
 * String param_4 = obj.getMinKey();
 */
```


# binheap
binary heap in DSSL2
#lang dssl2

interface PRIORITY_QUEUE[X]:
    # Returns the number of elements in the priority queue.
    def len(self) -> nat?

    # Returns the smallest element; error if empty.
    def find_min(self) -> X

    # Removes the smallest element; error if empty.
    def remove_min(self) -> NoneC

    # Inserts an element; error if full.
    def insert(self, element: X) -> NoneC


# Class implementing the PRIORITY_QUEUE ADT as a binary heap.
class BinHeap[X] (PRIORITY_QUEUE):
    let _data: VecC[OrC(X, NoneC)]
    let _len:  nat?
    let _lt?:  FunC[X, X, bool?]

    # Constructs a new binary heap with the given capacity and
    # less-than function for type X.
    def __init__(self, capacity, lt?):
        self._data = [ None; capacity ]
        self._len  = 0
        self._lt?  = lt?

    def len(self):
        return self._len

    def insert(self, new_element):
        if self._len == self._data.len():
            error('Array full')
        self._data[self._len] = new_element
        self._len = self._len + 1
        self._bubble_up(self._len-1)

    def find_min(self):
        #if self._data is None: return error('empty')
        if self._len == 0: error('empty')
        else: return self._data[0]

    def remove_min(self):
        #println('remove1')
        if self._len == 0:
            #println('remove2')
            return
        if self._len == 1:
            self._data[0] = None
            self._len = self._len - 1
        else:
            self._data[0] = self._data[self._len - 1] #is it -1 or nothing?
            self._data[self._len - 1] = None
            self._len = self._len - 1
            self._percolate_down(0)
            
       # if self._data is not None:
        #    self._data[0] = self._data[self._len()]
         #   self._data[self._len()] = None
          #  if self._data[0] > self.
        #move last element to root posn, overwrite old copy with None, decrement size
        #restore invariant: can only move down from root (percolate)
            #find out if it has child less than itself
            #if has two kids, find smaller of the two (may have two, just left, none)
            #if child < element: swap element w/ smallest child, continue downward until element < kids

    # Restores the invariant between element at index i and
    # its parent, if necessary.
    def _bubble_up(self, i: nat?) -> NoneC:
        let p = _parent(i)
        while self._lt?(self._data[i], self._data[p]):
            self._swap(i, p)
            i = p
            p = _parent(i)

    # Restores the invariant between element at index i and
    # its children, if necessary.
    def _percolate_down(self, i: nat?) -> NoneC:
        #let com = '''
        let s = self._find_smaller_child(i)
        while self._lt?(self._data[s], self._data[i]):
            self._swap(i, s)
            i = s
            s = self._find_smaller_child(s)
        
          
        let com = '''
        def _percolate_down(self, i: nat?) -> NoneC:
        let s = self._find_smaller_child(i)
        let temp = 0
        while self._lt?(self._data[s], self._data[i]):
           # println('Before swap (percolate down): ' + self._data)
            temp = self._data[i]
            self._data[i] = self._data[s]
            self._data[s] = temp
            i = s
            s = self._find_smaller_child(s)
           # println('self._data[s] = ' + self._data[s] + '; self._data[i]  = ' + self._data[i])                      
           # println('After swap (percolate down): ' + self._data)
            '''
          
        #let s = self._find_smaller_child


    # If the element at index i has any children smaller than itself,
    # returns the index of the smallest child; if i has no children,
    # or none of the children is smaller than the element at i,
    # returns i
    def _find_smaller_child(self, i: nat?) -> nat?:
        let smallest = self._data[i] 
        let small_index = i
        let L = _left_child(i) #L is left child
        let R = _right_child(i) #R is right child
        if L < self._len: #if leftchild exists
                if self._lt?(self._data[L], smallest): #if leftchild smaller than element
                    smallest = self._data[L] #leftchild is smallest
                    small_index = L 
        if R < self._len: #else if rightchild exists
                if self._lt?(self._data[R], smallest): #if rightchild smaller  
                    smallest = self._data[R] 
                    small_index = R
        return small_index


    # Swaps the elements at indices i and j.
    def _swap(self, i: nat?, j: nat?) -> NoneC:
        let a = self._data[i]
        self._data[i] = self._data[j]
        self._data[j] = a



# Computes the index of the left child of the given index.
def _left_child(i: nat?) -> nat?:
    return i + i + 1 


# Computes the index of the right child of the given index.
def _right_child(i: nat?) -> nat?:
    return i + i + 2


# Computes the index of the parent of the given index.
def _parent(i: nat?) -> nat?:
    return (i - 1)/2



##
##

test 'insert, insert, remove_min':
    let h = BinHeap[nat?](10, λ x, y: x < y)
    h.insert(1)
    assert h.find_min() == 1
    h.insert(2)
    assert h.find_min() == 1
    h.remove_min()
    assert h.find_min() == 2

test 'insert 10 elements, then remove':
    let h = BinHeap[nat?](10, λ x, y: x < y)
    for x in [1, 6, 8, 2, 5, 3, 4, 2, 2, 1]: h.insert(x)
    assert h.len() == 10
    #println('1')
    assert h.find_min() == 1
    #println('2')
    h.remove_min()
    assert h.len() == 9
    #println('3')
    assert h.find_min() == 1
    h.remove_min()
    #println('4')
    assert h.find_min() == 2
    h.remove_min()
    #println('5')
    assert h.find_min() == 2
    #println('5.1')
    h.remove_min()
    #println('5.2')
    assert h.find_min() == 2
    #println('5.3')
    h.remove_min()
    #println('7')
    assert h.find_min() == 3
    h.remove_min()
    assert h.find_min() == 4
    #println('9')
    h.remove_min()
    assert h.find_min() == 5
    h.remove_min()
    assert h.find_min() == 6
    #println('11')
    h.remove_min()
    assert h.find_min() == 8
    #println('12')
    h.remove_min()
    #println('13')
    assert h.len() == 0


# Sorts a vector of X, given a less-than function for X.
#
# This function performs a heap sort by inserting all of the
# elements of v into a fresh heap, then removing them in
# order and placing them back in v.
def heap_sort[X](v: VecC[X], lt?: FunC[X, X, bool?]) -> NoneC:
    let bh = BinHeap[X](v.len(), lt?)
    for i in v.len():
        bh.insert(v[i])
        #println(bh) #debug
    for i in v.len():
        v[i] = bh.find_min()
        #println(v) #debug
        bh.remove_min()
        #println(bh) #debug 
#### my function is 7 lines ####


test 'heap sort ascending':
    let v = [3, 6, 0, 2, 1]
    heap_sort(v, λ x, y: x < y)
    assert v == [0, 1, 2, 3, 6]

test 'heap sort descending':
    let v = [3, 6, 0, 2, 1]
    heap_sort(v, λ x, y: x > y)
    assert v == [6, 3, 2, 1, 0]

test 'heap sort strings':
    let v = ['foo', 'bar', 'baz', 'qux', 'quux']
    heap_sort(v, λ x, y: x < y)
    assert v == ['bar', 'baz', 'foo', 'quux', 'qux']
    
test 'tests':
    let h = BinHeap[nat?](10, λ x, y: x < y)
    let i = BinHeap[nat?](10, λ x, y: x < y)
    #println(i)
    #i.remove_min()
    let a = 0
    while a<10:
        h.insert(a)
        i.insert(9-a)
        a = a+1
    assert h.find_min() == 0
    assert i.find_min() == 0
    #println(i)
    #println(h)
    assert_error h.insert(4) #try to insert into a full BinHeap
    
    let g = [None]
    heap_sort(g, λ x, y: x < y)
    assert g == [None]
    
    def lt_as_str?(a, b): #defines a function that can take None (compares values of strings)
        return str(a) < str(b)
    let v = [ None, 'Naptime', 'No', True, 5, 7, 56 ]
    heap_sort(v, lt_as_str?)
    assert v == [ 5, 56, 7, 'Naptime', 'No', None, True ]
    
    let f = BinHeap(8, lambda x, y: (x is None and y is not None) or x < y) #another function that takes None
    f.insert(None)
    f.insert(None)
    assert f.len() == 2
    assert f.find_min() == None
    f.remove_min()
    assert f.len() == 1
    assert f.find_min() == None
    f.remove_min()
    assert f.len() == 0
    #println(f)
    assert_error f.find_min()
    #assert f.find_min() == None
    
    let b = BinHeap[nat?](10, λ x, y: x < y)
    for i in range(10):
        b.insert(1)
    assert b.len() == 10
    assert b.find_min() == 1
    b.remove_min()
    assert b.find_min() == 1
    
    let d = BinHeap[nat?](5, λ x, y: x < y)
    d.insert(1)
    d.insert(0)
    d.insert(3)
    d.insert(0)
    assert d.len() == 4
    #println(d)
    assert d.find_min() == 0
    d.remove_min()
    #println(d)
    assert d.find_min() == 0
    d.remove_min()
    #println(d)
    assert d.find_min() == 1    

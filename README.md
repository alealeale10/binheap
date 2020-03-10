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

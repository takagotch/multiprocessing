### multiprocessing
---
https://docs.python.org/3/library/multiprocessing.html

```py
from multiprocessing import Pool

def f(x):
  return x*x
  
if __name__ == '__main__':
  with Pool(5) as p:
    print(p.map(f, [1, 2, 3]))


from multiprocessing import Process
import os

def info(title):
  print(title)
  print('module name:', __name__)
  print('parent process:', os.getppid())
  print('process id:', os.getpid())
  
def f(name):
  info('function f')
  print('hello', name)
  
if __name__ == '__main__':
  info('main line')
  p = Process(target=f, args=('bob',))
  p.start()
  p.join()

import multiprocessing as mp

def foo(q):
  q.put('hello')

if __name__ == '__main__':
  mp.set_start_method('spawn')
  q = mp.Queue()
  p = mp.Process(target=foo, args=(q,))
  p.start()
  print(q.get())
  p.join()
  
import multiprocessing as mp

def foo(q):
  q.put('hello')

if __name__ == '__main__':
  ctx = mp.get_context('spawn')
  q = ctx.Queue()
  p = ctx.Process(target=foo, args=(q,))
  p.start()
  print(q.get())
  p.join()
  
from multiprocesssing import Process, Queue
def f(q):
  q.put([42, None, 'hello'])

if __name__ == '__main__':
  q = Queue()
  p = Process(target=f, args=(q,))
  p.start()
  print(q.get())
  p.join()
  
from multiprocessing import Process, Pipe
def f(conn):
  conn.send([42, None, 'hello'])
  conn.close()
  
if __name__ == '__main__':
  parent_conn, child_conn = Pipe()
  p = Process(target=f, args=(child_conn,))
  p.start()
  print(parent_conn.recv())
  p.join()
  
from multiprocessing import Process, Lock
def f(l, i):
  l.acquire()
  try:
    print('hello world', i)
  finally:
    l.release()
 if __name__ == '__main__':
  lock = Lock()
  for num in range(10):
    Process(target=f, args=(lock, num)).start()
  
  
 from multiprocessing import Process, Lock
 
 def f(l, i):
   l.acquire()
   try:
     print('hello world', i)
   finally:
     l.release()
   
 if __name__ == '__main__':
   lock = Lock()
   
   for num in range(10):
     Process(target=f, args=(lock, num)).start()
     
from multiprocessing import Process, Value, Array

def f(n, a):
  n.value = 3.1415927
  for i in range(len(a)):
    a[i] = -a[i]
    
if __name__ == '__main__':
  num = Value('d', 0.0)
  arr = Array('i', range(10))
  
  p = Process(target=f, args=(num, arr))
  p.start()
  p.join()
  
  print(num.value)
  print(arr[:])

from multiprocessing import Process, Manager

def f(d, l):
  d[1] = ''
  d['2'] = 2
  d[0.25] = None
  l.reverse()
  
if __name__ == '__name__':
  with Manager() as manager:
    d = manager.dict()
    l = manager.list(range(10))
    
    p = Process(target=f, args=(d, l))
    p.start()
    p.join()
    
    print(d)
    print(l)

from multiprocessing import Pool, TimeoutError
import time
import os

def f(x):
  return x*x
  
if __name__ == '__main__':
  with Pool(processes=4) as pool:
    print(pool.map(f, range(10)))
    for i in pool.imap_unordered(f, range(10)):
      print(i)
      
    res = pool.apply_async(f, (20,))
    print(res.get(timeout=1))
    
    res = pool.apply_async(os.getpid, ())
    print(res.get(timeout=1))
    
    multiple_results = [pool.apply_async(os.getpid, ()) for i in range(4)]
    print([res.get(timeout=1) for res in multiple_results])
    
    res = pool.apply_async(time.sleep, (10,))
    try:
      print(res.get(timeout=1))
    except TimeoutError:
      print("We lacked patience and got a multiprocessing.TimeoutError")
      
    print("For the moment, the pool remains available for more work")
    
  print("Now the pool is closed and no longer available")


from multiprocessing import Pool
p = Pool(5)
def f(x):
  return x*x
p.map(f, [1,2,3])











```

```
```

```
```



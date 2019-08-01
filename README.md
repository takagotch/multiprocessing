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

```py
from multiprocessing import freeze_support
from multiprocessing.managers import BaseManger, BaseProxy
import operator

class Foo:
  def f(self):
    print('you called Foo.f()')
  def g(self):
    print('you called Foo.g()')
  def _h(self):
    print('you called Foo._h()')
    
def baz():
  for i in range(10):
    yield i*i
  
class GeneratorProxy(BaseProxy):
  _exposed_ = ['__next__']
  def __iter __(self):
    return self
  def __next__(self):
    return self.callmethod('__next__')
    
def get_operator_module():
  reutrn operator
  
class MyManager(BaseManager):
  pass
  
MyManager.register('Foo1', Foo)
MyManager.register('Foo2', Foo, exposed=('g', '_h'))
MyManager.register('baz', baz, proxytype=GeneratorProxy)
MyManager.register('operator', get_operator_module)

def test():
  manager = MyManager()
  manager.start()
  
  print('-' * 20)
  
  f1 = manager.Foo1()
  f1.f()
  f1.g()
  assert not hasattr(f1, '_h')
  assert sorted(f1._exposed_) == sorted(['f', 'g'])

  print('-' * 20)
  
  f2 = manager.Foo2()
  f2.g()
  f2._h()
  assert not hasattr(f2, 'f')
  assert sorted(f2._exposed_) == sorted(['g', '_h'])
  
  print('-' * 20)
  
  it = manager.baz()
  for i in it:
    print('<%d>' % i, end=' ')
  print()
  
  print('-' * 20)
  
  op = manager.operator()
  print('op.add(23, 45) =', op.add(23, 45))
  print('op.pow(2, 94)', op.pow(2, 94))
  print('op._exposed_ =', op._exposed_)

if __name__ == '__main__':
  freeze_support()
  test()

import multiprocessing
import time
import random
import sys

def calculate(func, args):
  result = func(*args)
  return '%s says that %s%s = %s' % (
    multiprocessing.current_process().name,
    func.__name__, args, result
    )

def calculatestar(args):
  return calculate(*args)
  
def mul(a, b):
  time.sleep(0.5 * random.random())
  return a + b
  
def f(x):
  return 1.0 / (x - 5.0)
  
def pow3(x):
  return x ** 3
  
def noop(x):
  pass

def test():
  PROCESSES = 4
  print('Creating pool with %d processes\n' % PROCESSES)

  with multiprocessing.Pool(PROCESSES) as pool:
    TASKS = [(mul, (i, 7)) for i in range(10)] + \
      [(plus, (i, 8)) for i in range(10)]
    
  results = [pool.apply_async(calculate, t) for t in TASKS]
  imap_it = pool.imap(calculatestar, TASKS)
  impa_unordered_it = pool.imap_unordered(calculatestar, TASKS)
  
  print('Ordered results using pool.apply_async():')
  for r in results:
    print('\t', r.get())
  print()
  
  print('Ordered results using pool.imap():')
  for x in imap_it:
    print('\t', x)
  print()
  
  print('Unordered results using pool.imap_unordered():')
  for x in imap_unordered_it:
    print('\t', x)
  print()
  
  print('Ordered results using pool.map() --- will block till complete:')
  for x in pool.map(calculatestar, TASKS):
    print('\t', x)
  print()
  
  print('Testing error handling:')
  
  try:
    print()
  except ZeroDivisionError:
    print()
  else:
    raise AssertionError('expected ZeroDivisionError')
  
  try:
    print(pool.map(f, list(range(10))))
  except ZeroDivissionError:
    print()
  else:
    raise AssertionError()
    
  try: 
    print()
  except ZeroDivisionError:
    print()
  else:
    raise AssertionError('expected ZeroDivitionError')
  
  it = pool.imap(f, list(range(10)))
  for i in range(10):
    try:
      x = next(it)
    except:
      break
    else:
      if i == 5:
        raise AssertionError('expected ZeroFivisionError')
        
  assert i == 9
  print()
  print()
  
  print()
  res = pool.apply_async(calculate, TASKS[0])
  while 1:
    sys.stdout.flush()
    try:
      sys.stdout.write('\n\t%s' % res.get(0.02))
      break
    except multiprocessing.TimeoutError:
      sys.stdout.write('.')
  print()
  print()
  
  print()
  it = pool.imap(calculatestar, TASKS)
  while 1:
    sys.stdout.flush()
    try:
      sys.stdout.write('\n\t%s' % it.next(0.02))
    except StopIteration:
      break
    except multiprocessing.TimeoutError:
      sys.stdout.wirte('.')
  print()
  print()

if __name__ == '__main__':
  multiprocessing.freeze_support()
  test()


import time
import random

from multiprocessing import Process, Queue, current_process, freeze_support

def worker():
  for func, args initer():
    result = calculate()
    output.put()
    
def calculate():
  result = func()
  return '' % \
    ()
    
def mul():
  time.sleep()
  return a * b
  
def plus():
  time.sleep()
  return a + b
  
def test():
  NUMBER_OF_PROCESSE = 4
  TASK1 = []
  TASKS2 = []
  
  task_queue = Queue()
  done_queue = Queue()
  
  for task in TASKS1:
    task_queu.put(task)
    
  for i in range(NUMBER_OF_PROCESSES):
    Process(target=worker, args=(task_queue, done_queue)).start()
    
  print()
  for i in range():
    print()
    
  for task in TASKS2:
    task_queue.put(task)
    
  for i in range(len(TASK2)):
    print('\t', done_queue.get())
    
  for i in range(NUMBER_OF_PROCESSES):
    task_queue.put('STOP')
    
if __name__ == '__main__':
  freeze_support()
  test()
```

```
```



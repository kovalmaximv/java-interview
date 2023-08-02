# Java Collections
### Collections hierarchy
![collections_hierarchy.png](png/collections_hierarchy.png)

### В чем разница между классами java.util.Collection и java.util.Collections?
java.util.Collections - набор статических методов для работы с коллекциями.  
java.util.Collection - один из основных интерфейсов Java Collections Framework.

### Как между собой связаны Iterable и Iterator?
Интерфейс Iterable имеет только один метод - iterator(), который возвращает Iterator.

### Итераторы: fail-fast и fail-safe
**fail-fast** поведение означает, что при возникновении ошибки или состояния, которое может привести к ошибке, 
система немедленно прекращает дальнейшую работу и уведомляет об этом. Использование fail-fast подхода позволяет избежать 
недетерминированного поведения программы в течение времени.

В Java Collections API некоторые итераторы ведут себя как fail-fast и выбрасывают ConcurrentModificationException, 
если после его создания была произведена модификация коллекции, т.е. добавлен или удален элемент напрямую из коллекции, 
а не используя методы итератора.

В противоположность fail-fast, итераторы **fail-safe** не вызывают никаких исключений при изменении структуры, потому 
что они работают с клоном коллекции вместо оригинала.

### Чем различаются Enumeration и Iterator.
Хотя оба интерфейса и предназначены для обхода коллекций между ними имеются существенные различия:
+ с помощью Enumeration нельзя добавлять/удалять элементы;
+ в Iterator исправлены имена методов для повышения читаемости кода;
+ Enumeration присутствуют в устаревших классах, таких как Vector/Stack, тогда как Iterator есть во всех современных 
классах-коллекциях.

### Как поведёт себя коллекция, если вызвать iterator.remove()?
Если вызову iterator.remove() предшествовал вызов iterator.next(), то iterator.remove() удалит элемент коллекции, 
на который указывает итератор, в противном случае будет выброшено IllegalStateException().

### FIFO & FILO
FIFO - Queue  
FILO - Stack

### Сложность операций
**ArrayList**  
add(element) - O(1). С созданием нового массива O(N)  
add(element, index) - O(N)  
get(index) - O(1)  
remove(index) - O(N)  

**LinkedList**  
add(element) - O(1)  
add(element, index) - O(N)  
get(index) - O(N)  
remove(index) - O(N)  

**HashMap, LinkedHashMap**  
get(key) - O(1), если нет коллизии. Если есть - O(logN).  
put(key, element) - O(1), если нет коллизии. Если есть - O(logN).  

**TreeMap**  
get(key) - O(logN).  
put(key, element) - O(logN).  

**HashSet, LinkedHashSet**  
add(), remove(), contains() - O(1)  

**TreeSet**  
add(), remove(), contains() - O(logN)

### ArrayList vs LinkedList
ArrayList это список, реализованный на основе массива, а LinkedList — это классический двусвязный список, основанный 
на объектах со ссылками между ними.

ArrayList:
+ Доступ по индексу за O(1)
+ Вставка и удаление за O(n) в худшем случае, если придется переставлять все элементы и расширять массив.
+ На современных ОС сдвиг массива происходит быстро.

LinkedList:
+ Получение по индексу за O(n)
+ Вставка и удаление за O(1), но для вставки в середине нужно найти элемент по индексу за O(n)

### Зачем нужен HashMap, если есть Hashtable?
+ Методы класса Hashtable синхронизированы, что приводит к снижению производительности, а HashMap - нет;
+ HashTable не может содержать элементы null, тогда как HashMap может содержать один ключ null и любое количество 
значений null;
+ Iterator у HashMap, в отличие от Enumeration у HashTable, работает по принципу «fail-fast» 
(выдает исключение при любой несогласованности данных).

Hashtable это устаревший класс и его использование не рекомендовано.

### Как устроен HashMap?
В общем случае операции добавления, поиска и удаления элементов занимают константное время.

HashMap состоит из «корзин» (bucket). С технической точки зрения «корзины» — это элементы массива, которые хранят 
ссылки на списки элементов. При добавлении новой пары «ключ-значение», вычисляет хэш-код ключа, на основании которого 
вычисляется номер корзины (номер ячейки массива), в которую попадет новый элемент. Если корзина пустая, то в нее 
сохраняется ссылка на вновь добавляемый элемент, если же там уже есть элемент, то происходит последовательный переход 
по ссылкам между элементами в цепочке, в поисках последнего элемента, от которого и ставится ссылка на вновь 
добавленный элемент. Если в списке был найден элемент с таким же ключом, то он заменяется.

Помимо capacity у HashMap есть еще поле loadFactor, на основании которого, вычисляется предельное количество занятых 
корзин capacity * loadFactor. По умолчанию loadFactor = 0.75. По достижению предельного значения, число корзин 
увеличивается в 2 раза и для всех хранимых элементов вычисляется новое «местоположение» с учетом нового числа корзин.

![hashmap.png](png/hashmap.png)

Алгоритм добавления элемента:
1) Сначала ключ проверяется на равенство null. Если это проверка вернула true, будет вызван метод putForNullKey(value)
2) Далее генерируется хэш на основе ключа.
3) С помощью метода indexFor(hash, tableLength), определяется позиция в массиве, куда будет помещен элемент.
4) Теперь, зная индекс в массиве, мы получаем список (цепочку) элементов, привязанных к этой ячейке. Хэш и ключ нового 
элемента поочередно сравниваются с хэшами и ключами элементов из списка и, при совпадении этих параметров, значение 
элемента перезаписывается.
5) Если же предыдущий шаг не выявил совпадений, будет вызван метод addEntry(hash, key, value, index) для добавления 
нового элемента.

### Как устроен LinkedHashMap
Идентично HashMap, но каждый Entry хранит указатель на предыдущий и следующий Entry.

### Как устроен TreeMap
TreeMap работает на основе красно-черного дерева, хеш не используется. Ключ используется для навигации и сортировки по
дереву.

### Как устроен любой Set
Аналогично идентичной реализации Map, но хранится только ключ, без значения.

### В чем отличия TreeSet и HashSet?
TreeSet обеспечивает упорядоченно хранение элементов в виде красно-черного дерева. Сложность выполнения основных 
операций не хуже O(log(N)) (Логарифмическое время).

HashSet использует для хранения элементов такой же подход, что и HashMap, за тем отличием, что в HashSet в качестве 
ключа и значения выступает сам элемент, обеспечивает временную сложность выполнения операций аналогично HashMap.

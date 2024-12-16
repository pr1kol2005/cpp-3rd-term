# Array

В этой задаче вам нужно реализовать массив. В отличии от std::array, мы позволим ему расширяться. Сигнатура array:
```
template <typename T, std::size_t Extent, template <typename> typename Creation>
class Array;
```
**T** - тип, хранимый в массиве

**Extent** - Сколько элементов в нем храниться. Может принять std::dynamic_extent. Если extent != std::dynamic_extent, то все его элементы должны храниться на стеке. В случае с std::dynamic_extent вы не знаете в compile time, сколько элементов нужно в нем хранить, поэтому будет требование на реализацию, аналогичной std::vector - расширение.

**Creation** - стратегия создания объекта.

---

Для Array нужно поддержать следующие операции:
- [i] - доступ к элементу по индексу
- at(i) - доступ по индексу с обработкой границ
- data() - доступ к указателю на элементы
- front()
- back()
- методы для работы с итераторами:
   	- begin()
   	- end()
   	- cbegin()
   	- cend()
   	- rbegin()
   	- rend()
   	- crbegin()
   	- crend()
- size() - Возвращает число элементов
- empty()
- Для dynamic extent:
    - push_back(T) - вставить в конец элемент
    - pop_back() - удалить с конца элемент
    - сapacity() - количество зарезервированной памяти
    - resize(n) - именить размер массива
    - reserve(n) - выделяет достаточно места под n элементов
    - clean() - Чистит массив
    - shrink_to_fit() - Уменьшает количество выделенной памяти до size()

---

CreationStrategy отвечает за возможные способы создания объекта. У Array должны быть только private конструкторы, кроме, возможно copy constructor. У Array должен быть статический метод сreate, который будет его создавать. create должен иметь возможность напрямую вызывать конструкторы вектора. (Необходимые конструкторы см в тестах)
Нужно реализовать 3 стратегии:
- DefaultCreation:
	- Если выбрана эта стратегия create выдает обычный объект
- StaticSingleton:
	- Array не должен быть копируемым.
	- craete возвращает ссылку на объект Array. При попытке вызвать create второй раз c другими аргументами - нужно кинуть исключение. При вызове с теми же аргументами нужно вернуть ссылку на тот же объект
 - CountedCreation:
	- Работает аналогично DefaultCreation.
	- Добавляет статический метод get_created_count(), возвращаюий сколько объектов было созданно

---

Для dynamic extent нужно поддержать различные способы выделения памяти. Для этого в конструкторы Array последним параметром прокидывается MemoryResource* с параметром по умолчанию GetDefaultResource(). MemoryResource - абстратный класс, который умеет 2 метода: allocate(std::size_t) и deallocate(void*) - методы выделяющие и очищающие память. Вам нужно реализовать 2 MemoryResource:
- NewDeleteResource 
- MallocFreeResource
Так же должна быть функция SetDefaultResource(MemoryResource*) - определяет, что возвращает GetDefaultResource() и возвращает, что было DefaultResource до вызова. Если ее не вызывали, ресурс по умолчанию - NewDeleteResource

---

Для класса Array нужно реализовать несколько трейтов, реализованых посредством специализаций функций:
   - GetRank(Array) - возвращает количество вложенный Array (Array<Array\<int\>> - ответ 2)
   - GetTotalElements(array) - возвращает суммарное количество элементов в структуре (Array\<Array<int, 3>, 10\> - итого 30. Если хоть на каком то уровне встретилось std::dynamic_extent, то ответ - std::dynamic_extent)
   - GetExtent\<I\>(array) - возвращает extent I-го слоя

Задачу нужно реализовать на с++17, вместо константы std::dynamic_extent, появившейся в c++20, будет 
предложена аналогичная ей std::size_t kDynamicExtent;

Все, связанное с MemoryResource должно находиться в namespace memres, со стратегиями - в namespace strategy, с трейтами - namespace traits
Array должен быть доступен в global scope, все, что нужно для его реализации - namespace details

Вся задача должна быть реализованна в одном файле
#LINQ - синтаксис в .net-языках, спроектированный для работы с источниками данных, таких как - коллекции, базы данных, XML-документы и прочие.
#LINQ-запрос - это способ выполнения операций с данными, который интегрирован в язык.

Из чего состоит запрос:
1. Источник данных (Выбора источника данных),
2. Описание тела запроса,
3. Выполнение запроса.

Отложенное выполнение в LINQ - механизм, при котором запрос не выполняется в момент его создания, а откладывается до тех пор, пока к данным не будет фактического обращения.
Существуют методы, которые вызывают немедленное выполнение (Immediate Execution) запроса и загрузку данных в память. В основном это агрегатные функции и методы возвращения коллекции.

Пример:
```
int[] numbers = {1, 2, 3, 4, 5};

// Запрос создается, но не выполняется
var query = from n in numbers where n > Get1() select n;

// Запрос выполнится только здесь, когда мы начнем итерировать результаты
foreach(var number in query)
{
	Console.WriteLine(number);
}

numbers.Where(item => item > Get1()).Count;

int Get1() => return 2;
```

Провайдеры (область применения) данных, доступные в LINQ:
1. LINQ to Objects - применяется для работы с массивами и коллекциями,
2. LINQ to Entities - используется при обращении к базам данных через технологию Entity Framework,
3. LINQ to XML - применяется при работе с файлами XML,
4. LINQ to DataSet - применяется при работе с объектами DataSet,
5. Parallel LINQ (PLINQ) - используется для выполнения параллельных запросов.

Интерфейс IQueryable<Т>:
#IQueryable<Т> - интерфейс, который используется в LINQ для создания запросов к удаленным источникам данных.
![[Pasted image 20250124174026.png]]

1. Использовать #IQueryable - когда работаете с удаленными источниками данных, например, с базами данных или веб-сервисами.
2. Использовать #IEnumerable - когда данные уже находятся в памяти, например, в списке, массиве или другой коллекции.


Пример:
```
using (ApplicationContext db = new ApplicationContext())
{
	// Добавить пользователей в db
	User tom = new User {Name = "Tom", Age = 33};
	User alice = new User {Name = "Alice", Age = 26};
	db.Add(tom);
	db.Add<User>(alice);
	db.SaveChanges();
	
	IQueryable<User> query = db.Users;
	query = query.Where(item => item.Age > 27);
	query = query.Where(item => item.Age < 34);
	List<User> users = query.ToList();
}
```

### Варианты синтаксиса

1. Декларативный синтаксис/синтаксис запросов/SQL-подобный синтаксис:
![[Pasted image 20250124175156.png]]
При использовании синтаксиса всегда используются 3 предложения:
- from - указывает псевдоним элемента источника данных,
- in - указывает источник данных,
- select/group - возвращает элементы данных.

Ключевые слова:
1. from - где указывается элемент последовательности (обязателен), используется с in,
2. in - источник данных (обязателен),
3. where - задает фильтрацию элементов последовательности (опционально),
4. select - возвращает проекцию данных (обязательно, либо group),
5. group - возвращает объекты типа IGrouping<Тkey,TElement>; Используется с by (обязательно, либо select),
6. by - указывает параметр, по которому необходимо сгруппировать члены последовательности,
7. let - определяет переменную и присваивает ей значение, рассчитанное на основе значений данных,
8. into - служит для создания временного идентификатора для хранения результата group,
9. join - объединяет различные последовательности, имеющих отношения в объектной модели; Используется с on,
10. on - задает ключи, по которым необходимо сопоставить к коллекции в join,
11. equals - сравнивает значения в выражениях запроса; используется с join on,
12. orderby - сортирует последовательности значений, используется компаратором по умолчанию, 
13. ascending/descending - сортируется последовательность значений, используется компаратором по умолчанию.

Правила описания за проса:
1. Выражение должно начинаться с ключевого слова from и должно заканчиваться select или group,
2. Между первым и последним предложением могут находиться where, orderby, join, let. Может быть дополнительный from, может быть into, join group,
3. Выражение может возвращать 2 типа: var или IEnumerable<Т>,
4. Запросы могут иметь вложенность.

Пример запроса:
```
var query =
	from item in source
	where item.Department.DepartmentName.Equals("ИТ")
	orderby item.Name /*ascending*/ /*descending*/
	select item;
```

Примеры:
1. Без условий:
```
asIs =
	from item in source
	select item;
```
2. Демонстрация фильтров:
```
asIs =
	from item in source
	where item.Department.DepartmentName.Equals("ИТ")
	select item
```
3. Сортировка orderby:
```
IEnumerable<Employer> orderBy =
	from item in source
	orderby item.Name
	select item;
```
4. group by:
```
IEnumerable<IGrouping<int, Employer>> employee =
	from item in source
	group item by item.Department.Id;
```
5. Использование let:
```
var emplInDeparms =
	from item in source
	let description = ($"{item.Name} работает в отделе: {item.Deparment}") // либо source.Count()
	// select description;
	select new {id = item.Id, Name = item.Name, Department = item.Department, Description = description, Count = description}
```


7. Fluent API/Метод-синтаксис/Недекларативный синтаксис
![[Pasted image 20250124175403.png]]
При использовании, используются методы расширения интерфейса IEnumerable<Т>.

Все методы fluent API на основе LINQ:
1. Принимают, как правило обобщенный делегат (анонимные функции) в качестве параметра. Могут принимать переменную делегата в явном виде,
2. Возвращают последовательность или одно значение,
3. Могут принимать еще одну коллекцию как параметр (Join).

Методы расширения IEnumerable<Т>:
Функционал метод-синтаксиса идентичен функционалу декларативного синтаксиса с той разницей, что:
1. Нет ключевых слов - from, in, let, into,
2. Ключевые слова операторов по запросу созвучны по написанию, но работают посредством обобщенных делегатов - Action, Predicate, Func.

Пример:
```
var query = source.Where(item => item.Id > 1).Select(item => item);
```


Особенности:
1. Декларативный подход не поддерживает:
- Агрегатные функции: Sum(), Count(), Max(), Min(), Average();
- Методы пагинации: Skip(), SkipWhile(), Take(), TakeWhile();
- Методы для работы с порядком элементов: ThenBy(), ThenByDescending();
- Методы для работы с множествами: Distinct(), Union(), Intersect(), Except();
- Методы группового соединения: GroupJoin();
- Экзистенциальные запросы: Any(), All(), Contains().

2. Fluent API(метод-синтаксис):
- Запросы с ключевым словом let;
- Запросы с ключевым словом into.
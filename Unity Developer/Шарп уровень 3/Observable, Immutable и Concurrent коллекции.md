#Observable - это объединение List и паттерна Observable. Это позволяет организовывать подписку на события изменения коллекции.

Для работы с Observable используется - ObservableCollection.
1. List + Observable паттерн,
2. Позволяет организовать подписку на события изменения коллекции,
3. Содержит NotifyCollectionChangedEventHandler? CollectionChanged для реагирования на изменения коллекций.

Пример:
```
using System.Collections.ObjectModel;
using System.Collections.Specialized;

internal class Program
{

    private static void Main(string[] args)
    {
        var observableCollection = new ObservableCollection<int>();
        observableCollection.CollectionChanged += ProcessCollectionChangedEvent;

        for (int i = 0; i < 5; i++)
            observableCollection.Add(i);

        Log(observableCollection);

        observableCollection.Remove(3);
        Log(observableCollection);

        observableCollection[2] = 100;
        Log(observableCollection);

        observableCollection.Clear();
    }

    private static void ProcessCollectionChangedEvent(object? sender, NotifyCollectionChangedEventArgs e)
    {
        switch (e.Action)
        {
            case NotifyCollectionChangedAction.Add:
                Console.WriteLine($"Добавлен элемент {e.NewItems[0]}");
                break;
            case NotifyCollectionChangedAction.Remove:
                Console.WriteLine($"Удален элемент {e.OldItems[0]}");
                break;
            case NotifyCollectionChangedAction.Reset:
                Console.WriteLine($"cleared");
                break;
            case NotifyCollectionChangedAction.Replace:
                Console.WriteLine($"Заменен элемент {e.OldItems[0]} на элемент {e.NewItems[0]}");
                break;
        }
    }

    static void Log<T>(IEnumerable<T> values)
    {
        Console.WriteLine($"[ {String.Join(", ", values)} ]");
    }


    static List<T> Limit<T>(List<T> s, int limit = 1)
    {
        var res = new List<T>(limit);

        for (int i = 0; i < limit && i < s.Count; i++)
            res.Add(s[i]);

        return res;
    }
}
```

### Concurrent collections

Проблема:
1. Простая коллекция - какой поток создал коллекцию, тот ей и управляет,
2. Можно ускорить работу с коллекцией - несколько потоков,
3. Нужно организовать синхронизацию доступа к коллекции,
4. Простая коллекция - не подходит, непредсказуемое поведение.

Решение - concurrent collections.

Свойства :
1. В общем случае - медленнее не-concurrent,
2. API для защиты от "гонки потоков" (race condition),
3. Можно безопасно перебирать элементы foreach.

Пример:
```
using System.Collections.Concurrent;
using System.Collections.ObjectModel;
using System.Collections.Specialized;

internal class Program
{

    static object _s = new object();
    static ConcurrentDictionary<string, long> _dic = new();

    private static void Main(string[] args)
    {
        ConcurrentBag<int> bag = new();


        Parallel.For(1, 10, DoWork); // запуск параллельного потока
        Log(_dic);
    }

    static void DoWork(int threadValue)
    {
        for (int i = 0; i < 10; i++)
        {
            var playerName = GetPlayerName(threadValue);
            _dic.AddOrUpdate(playerName, (key) => threadValue, (key, oldValue) => oldValue * key.Length);

            if (!_dic.ContainsKey(playerName))
	            _dic.Add(playerName, threadValue);
            else
               _dic[playerName] = _dic[playerName] * 10;
        }
    }


    static string GetPlayerName(int playerNumber)
    {
        return $" Player #{playerNumber}";
    }

    static void Log<T, S>(IEnumerable<KeyValuePair<T, S>> values)
    {
        Console.WriteLine($"[ {String.Join(", ", values.Select(x => x.Value))} ]");
    }

    static void Log<T>(IEnumerable<T> values)
    {
	    Console.WriteLine($"[ {String.Join(", ", values)} ]");
    }

}
```

Варианты:
1. ConcurrentDictionary.
2. ConcurrentBag,
3. ConcurrentStack,
4. ConcurrentQueue,
5. BlockingCollection.

### Immutable collections

Синопсис:
1. Классические коллекции (например, List) могут меняться в одном потоке,
2. В общем случае - удобно и безопасно,
3. Но иногда коллекция несет и сематический смысл (например, состояние базы данных),
4. Тогда использование изменяемых коллекций может повредить читаемость кода,
5. Для этого существуют Immutable (неизменяемый) интерфейс.

Варианты:
1. ImmutableStack,
2. ImmutableList,
3. ImmutableArray,
4. ImmutableDictionary.

Пример:
```
using System.Collections.Immutable;
using System.Collections.ObjectModel;
using System.Collections.Specialized;

internal class Program
{

    private static void Main(string[] args)
    {
        ImmutableList<int> ints =
        var t = ImmutableList<int>.Empty.AddRange(new[] { 1, 2, 3 });

        Log(t);
        var t1 = t.Add(100);

        Log(t1);

        Log(t.Add(200));

        Log(new List<int>() { 5, 7, 8 });
    }

    static void Log<T>(IList<T> values, T value = default(T))
    {
        values.Add(value);
        Console.WriteLine($"[ {String.Join(", ", values)} ]");
    }

}
```

Т.е. мы не можем изменить данную коллекцию, но мы можем редактировать ее и создадим новую коллекцию с внесенными изменениями.
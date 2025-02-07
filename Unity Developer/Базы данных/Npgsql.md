Работа с базой:

Создание объекта для подключения - ``NpgsqlConnection connection = new NpgsqlConnection(connectionString)
Открыть соединение - ``connection.Open();

Типы выполнения команд:
1. `ExecuteNonQueryAsync` - выполняет SQL-запрос,  который не возвращает никаких результатов, обычно это операторы `INSERT`, `UPDATE` или `DELETE`. Возвращает количество затронутых строк.
2. `ExecuteScalarAsync` - выполняет SQL, который возвращает единственное скалярное значение.
3. ExecuteReaderAsync - выполняет SQL-запрос, возвращающий полный набор результатов. Возвращает `NpgsqlDataReader`, который можно использовать для доступа к набору результатов.

Пример выполнения запроса выборки данных:
```
string query = "SELECT id, name FROM users";

using(NpgsqlCommand command = new NpsqlCommand(query, connection))
{

		using(NpgsqlDataReader reader = command.ExecuteReader())
		{
			while(reader.Read())
			{
				Console.WriteLine($"ID: {reader["id"]}, Name: {reader["name"]}");
			}
		}
}
```

Пример выполнения запроса вставки данных:
```
string query = "INSERT INTO users (name, age) VALUES (@Name, @Age)";

            using (NpgsqlCommand command = new NpgsqlCommand(query, connection))
            {
                // Добавление параметров
                command.Parameters.AddWithValue("@Name", "John Doe");
                command.Parameters.AddWithValue("@Age", 30);

                // Выполнение запроса
                int rowsAffected = command.ExecuteNonQuery();
                Console.WriteLine($"{rowsAffected} строк добавлено.");
            }
```

Пример асинхронных операций:
```
 using (NpgsqlConnection connection = new NpgsqlConnection(connectionString))
        {
            await connection.OpenAsync();

            string query = "SELECT id, name FROM users";

            using (NpgsqlCommand command = new NpgsqlCommand(query, connection))
            {
                using (NpgsqlDataReader reader = await command.ExecuteReaderAsync())
                {
                    while (await reader.ReadAsync())
                    {
                        Console.WriteLine($"ID: {reader["id"]}, Name: {reader["name"]}");
                    }
                }
            }
        }
```

### Использование Entity Framework Core

Необходимые пакеты:
4. Npgsql.EntityFrameworkCore.PostgreSQL;
5. Microsoft.EntityFrameworkCore.Tools;
6. Microsoft.EntityFrameworkCore;

Создание контекста базы данных:
```
using Microsoft.EntityFrameworkCore;

public class AppDbContext : DbContext
{
    public DbSet<User> Users { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseNpgsql("Host=localhost;Port=5432;Username=postgres;Password=yourpassword;Database=yourdatabase");
    }
}

public class User
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
}
```

Использование контекста для работы с БД:
```
using System;
using System.Linq;
using System.Threading.Tasks;

class Program
{
    static async Task Main(string[] args)
    {
        using (var context = new AppDbContext())
        {
            // Добавление записи
            var user = new User { Name = "John Doe", Age = 30 };
            context.Users.Add(user);
            await context.SaveChangesAsync();

            // Чтение записей
            var users = await context.Users.ToListAsync();
            foreach (var u in users)
            {
                Console.WriteLine($"ID: {u.Id}, Name: {u.Name}, Age: {u.Age}");
            }
        }
    }
}
```

### Транзакции
Для группировки нескольких операций в одну транзакцию `BeginTransaction()`:

```
using System;
using Npgsql;

class Program
{
    static void Main(string[] args)
    {
        string connectionString = "Host=localhost;Port=5432;Username=postgres;Password=yourpassword;Database=yourdatabase";

        using (NpgsqlConnection connection = new NpgsqlConnection(connectionString))
        {
            connection.Open();

            using (var transaction = connection.BeginTransaction())
            {
                try
                {
                    string query1 = "INSERT INTO users (name, age) VALUES ('Alice', 25)";
                    string query2 = "INSERT INTO users (name, age) VALUES ('Bob', 35)";

                    using (var command1 = new NpgsqlCommand(query1, connection))
                    using (var command2 = new NpgsqlCommand(query2, connection))
                    {
                        command1.ExecuteNonQuery();
                        command2.ExecuteNonQuery();
                    }

                    transaction.Commit();
                    Console.WriteLine("Транзакция завершена успешно.");
                }
                catch (Exception ex)
                {
                    transaction.Rollback();
                    Console.WriteLine($"Ошибка: {ex.Message}");
                }
            }
        }
    }
}
```
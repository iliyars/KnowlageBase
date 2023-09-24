**Планировщик задач** - это механизм, который позволяет настроить выполнение задач указанным способб и методами.

Для работы с планировщиком задач в .NET используют класс `TaskSheduler`. Это базовый абстрактный класс.

### Основные методы планировщика

#### Абстрактные методы
- `QueueTask(Task task)` - помещает переданую задачу в очередь выполнения.
- `GetSchedulerTask()` - возвращает очередь в виде коллекции.
- `TreExecuteTaskInline(Task task, bool taskWasPreviouslyQueued)` - запрашивает возможность выполниться синхронно.
#### Виртуальные методы
- `TryExecuteTask(Task task)` - попытка выполнить задачу.
- `Trydequeue(Task task)` - попытка удалить из очереди выполнения.
- `FromCurrentSynchronisationContext()` - создает планировщик, связанный с текущим элементом SynchronisationContext.
####  Свойства
- Default - стандартный планировщик, построенный на пуле потоков.
- Current - выдает текущего  планировщика.
- MaximumConcurrenyLevel - максимальный уровень параллелизма.
- Id - идентификатор планировщика.

В основном методы `internal`, а это значит, что они будут вызываться внутри сборки `mscorelib`

### Стандартный планировщик задач `SynchronizationContextTaskSheduler`
 
`SynchronizationContext` - это базовый класс, который  предоставляет способ размещения выполнения участков кода в очереди контекста. Т.е можно передать код для исполнения из одного контекста в другой.

Он помогает решать такю проблемму, как обращение к элементу управления из потока, котрый его не создавал. (например conrol'ы) 

пример:
```cs
private void Worker()
        {
	        //Планировщик с контекстом выполнения
            TaskScheduler sheduler = TaskScheduler.FromCurrentSynchronizationContext();

            Task<int>[] tasks = new Task<int>[20];

            new Task(() =>
            {
                for (int i = 0; i < tasks.Length; i++)
                {
                    int m = i;
                    tasks[i] = new Task<int>(() =>
                    {
                        Thread.Sleep(1000);
                        return m;
                    });
                    tasks[i].Start();

                    tasks[i].ContinueWith(t =>
                    {

                        Dispatcher.Invoke(() =>
                        {
                            txtContinuation.Text += $"Результат - {t.Result} в потоке [{Thread.CurrentThread.ManagedThreadId}]";
                            txtContinuation.Text += Environment.NewLine;

                        });
                        
                    });
                }
                Task.WaitAll(tasks);
                Thread.Sleep(2000);
                flag = false;
            }, TaskCreationOptions.LongRunning).Start();
             
            
   

          
        }
```

`Dispatcher.Invoke(Func<string>)` - выполняет задачу в контексте потока
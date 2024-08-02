# ASP.NET await 與 wait 的差異

當在 C# 進行非同步 (Asynchronous) 工作程式設計的時候，通常會看到有兩種選擇來等候非同步工作的完成，第一種就是使用 Task 類別所提供的 Wait() 方法，另外一種就是使用 C# 5.0 之後所提供的 await 關鍵字。

當您想要在程式碼中使用 await 關鍵字，這個方法必須加上 async 修飾詞，並且當方法加上了 async 修飾詞之後，這個方法的回傳值僅能夠為 Task / Task / void 這三者其中之一，若宣告了其他的回傳型別，就會造成編譯時候產生錯誤訊息；例如，當宣告了 `static async int My() { }` 這樣的方法，將會從編譯器得到這樣的錯誤訊息： `錯誤 CS1983 非同步方法的傳回類型必須為 void、Task 或 Task<T>`

首先，先來查看 await / wait 在劍橋字典上的定義，不論定義是甚麼，中文上可以翻譯成為等待或者等候，其實，從這樣的定義文字中，很難理解到在 C# 中這兩個用法的差異

[wait](https://dictionary.cambridge.org/zht/詞典/英語/wait)

```
to allow time to go by, especially while staying in one place without doing very much, until someone comes, until something that you are expecting happens or until you can do something
```

[wait](https://dictionary.cambridge.org/zht/詞典/英語/wait)

```
to wait for or be waiting for something
```

現在，撰寫一個測試範例小程式(如下所示)，來了解 Wait() 方法的運作模式與特性；當程式執行到 Wait() 方法之後，當前的執行緒就進入到封鎖狀態，但是，這樣會有甚麼問題呢？因為 Wait() 方法需要等待非同步工作的執行完成，若非同步工作沒有執行完畢，回報最終執行結果前，這個呼叫 Wait() 方法的執行緒就無法執行其他程式碼，因為他需要不斷的了解，非同步工作執行完成了沒。

這個測試程式首先會執行 DoWait() 這個方法，在這個方法內，將會先執行 Before() 方法，顯示出一段訊息文字，接著就會使用 Wait() 方法來等候非同步工作的完成，這裡是使用 `MyMethodAsync().Wait()` 這個敘述，在這個敘述中，首先會執行 MyMethodAsync() 方法，而在這個 MyMethodAsync() 方法內，直接回傳了一個 Task.Run 所產生的非同步工作之 Task 物件。

現在，因為 MyMethodAsync() 方法回傳了一個 Task 物件，因此，就可以使用 Wait() 方法來等候這個非同步工作的完成。

在 Main 方法內，看到當呼叫完成 DoWait() 方法之後，將會每隔 0.5 秒的時間，顯示一個訊息。

```csharp
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine($"主執行緒開始執行，" +
            $"ID={Thread.CurrentThread.ManagedThreadId}");
        Console.WriteLine("呼叫 DoWait 方法");
        DoWait();
        for (int i = 0; i < 10; i++)
        {
            Thread.Sleep(500);
            Console.WriteLine($"主執行緒正在處理其他事情({(i + 1) * 500}ms) " +
            $"(執行緒:{Thread.CurrentThread.ManagedThreadId})");
        }
        Console.WriteLine("Press any key for continuing...");
        Console.ReadKey();
    }
    static void DoWait()
    {
        Before();
        MyMethodAsync().Wait();
        After();
    }
    private static void Before()
    {
        Console.WriteLine($"  [Before] 呼叫 MyMethodAsync().Wait(); 前的" +
            $"執行緒:{Thread.CurrentThread.ManagedThreadId}");
    }
    private static void After()
    {
        Console.WriteLine($"  [After] 呼叫 MyMethodAsync().Wait(); 後的" +
            $"執行緒:{Thread.CurrentThread.ManagedThreadId}");
    }
    static Task MyMethodAsync()
    {
        Console.WriteLine($"進入到 MyMethodAsync 前，" +
            $"所使用的執行緒 :{Thread.CurrentThread.ManagedThreadId}");
        return Task.Run(() =>
        {
            Console.WriteLine($"MyMethodAsync 開始進行非同步工作，" +
                $"所使用的執行緒 :{Thread.CurrentThread.ManagedThreadId}");
            Console.WriteLine("需要花費 7 秒鐘");
            Thread.Sleep(7000);
        });
    }
}
```

底下為這個測試範例程式的執行結果，原則上，當 呼叫 MyMethodAsync().Wait() 敘述執行之後，另外一個背景執行緒就已經並行執行了，而要能夠接續執行 MyMethodAsync().Wait() 之後的程式碼，唯一的條件那就是要等到這個背景執行緒(也就是設計的非同步工作)執行完畢後，才能夠繼續網頁執行下去。

因此，將會看到，只有非同步工作中的程式碼，使用的是 執行緒 3 來執行，而其他的程式碼都會在執行緒1下來執行。

```
主執行緒開始執行，ID=1
呼叫 DoWait 方法
  [Before] 呼叫 MyMethodAsync().Wait(); 前的執行緒:1
進入到 MyMethodAsync 前，所使用的執行緒 :1
MyMethodAsync 開始進行非同步工作，所使用的執行緒 :3
需要花費 7 秒鐘
  [After] 呼叫 MyMethodAsync().Wait(); 後的執行緒:1
主執行緒正在處理其他事情(500ms) (執行緒:1)
主執行緒正在處理其他事情(1000ms) (執行緒:1)
主執行緒正在處理其他事情(1500ms) (執行緒:1)
主執行緒正在處理其他事情(2000ms) (執行緒:1)
主執行緒正在處理其他事情(2500ms) (執行緒:1)
主執行緒正在處理其他事情(3000ms) (執行緒:1)
主執行緒正在處理其他事情(3500ms) (執行緒:1)
主執行緒正在處理其他事情(4000ms) (執行緒:1)
主執行緒正在處理其他事情(4500ms) (執行緒:1)
主執行緒正在處理其他事情(5000ms) (執行緒:1)
Press any key for continuing...
```

好的，上述的測試範例小程式並不複雜，只需要以同步程式碼設計角度來理解，就能夠知道整個程式的運作流程，現在，要來了解 await 關鍵字的運作方式，並理解與 Wait() 方法有何不同。

現在，在 Main 方法內，將會有一個 DoAwait() 方法，所以，當程式碼執行到這個敘述的時候，將會進入到 DoAwait() 方法內。在其方法內，將會使用 await 關鍵字來等候 MyMethodAsync() 非同步工作的完成，使用的是這個 C# 敘述 `await MyMethodAsync();`。

awiat 關鍵字與 Wait() 方法最大的差異點就在於，當使用後者來等待一個非同步工作，呼叫端的執行緒因為要知道非同步工作是否已經完成了，所以，會進入到封鎖狀態下，持續地獲得非同步工作已經完成了；而當使用 await 關鍵字，當要等候非同步工作執行完畢的狀態時候，將會立即 retun 回到呼叫這個方法 (是這個 DoAwait 方法) 的地方，也就是會回到 Main 方法內呼叫 DoAwait 方法的點，注意，此時，主執行緒是沒有進入到封鎖的狀態，因此，主執行緒將會繼續執行呼叫 DoAwait() 之後的敘述，也就是會每隔 0.5 秒的時間，顯示一個訊息。

當主執行緒每隔 0.5 秒顯示一個訊息，並且做 10 次，也就是說，在 5 秒鐘內，將會顯示出十個訊息文字。就在此時，背景執行緒卻還在持續執行非同步的工作，這個非同步工作將會需要 7 秒鐘的時間才能夠完成。

因此，當 10 個 Main 方法內顯示的訊息文字顯示完畢之後(使用的執行緒1來執行)，非同步工作是還沒有完成的；而當非同步工作完成之後， await MyMethodAsync() 敘述之後的程式碼將會要繼續來執行，此時，將會呼叫 After() 方法。

現在，看到另外一個與呼叫 Wait() 方法的不同點，當呼叫 Wait() 方法之後，並且非同步工作執行完畢之後，呼叫 After() 方法，顯在是使用 執行緒1 這個執行緒來繼續執行；而在使用 await 關鍵字的時候，在 await 關鍵字之後的程式碼要繼續執行，在此是要呼叫 After() 方法，卻是得到了是使用 執行緒3 來繼續執行 await 之後的程式碼。

```csharp
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine($"主執行緒開始執行，" +
            $"ID={Thread.CurrentThread.ManagedThreadId}");
        Console.WriteLine("呼叫 DoAwait 方法");
        DoAwait();
        for (int i = 0; i < 10; i++)
        {
            Thread.Sleep(500);
            Console.WriteLine($"主執行緒正在處理其他事情({(i + 1) * 500}ms) " +
            $"(執行緒:{Thread.CurrentThread.ManagedThreadId})");
        }

        Console.WriteLine("Press any key for continuing...");
        Console.ReadKey();
    }

    static async void DoAwait()
    {
        Before();
        await MyMethodAsync();
        After();
    }

    private static void Before()
    {
        Console.WriteLine($"  [Before] 呼叫 MyMethodAsync().Wait(); 前的" +
            $"執行緒:{Thread.CurrentThread.ManagedThreadId}");
    }

    private static void After()
    {
        Console.WriteLine($"  [After] 呼叫 MyMethodAsync().Wait(); 後的" +
            $"執行緒:{Thread.CurrentThread.ManagedThreadId}");
    }

    static Task MyMethodAsync()
    {
        Console.WriteLine($"進入到 MyMethodAsync 前，" +
            $"所使用的執行緒 :{Thread.CurrentThread.ManagedThreadId}");
        return Task.Run(() =>
        {
            Console.WriteLine($"MyMethodAsync 開始進行非同步工作，" +
                $"所使用的執行緒 :{Thread.CurrentThread.ManagedThreadId}");
            Console.WriteLine("需要花費 7 秒鐘");
            Thread.Sleep(7000);
        });
    }
}
```



```
主執行緒開始執行，ID=1
呼叫 DoAwait 方法
  [Before] 呼叫 MyMethodAsync().Wait(); 前的執行緒:1
進入到 MyMethodAsync 前，所使用的執行緒 :1
MyMethodAsync 開始進行非同步工作，所使用的執行緒 :3
需要花費 7 秒鐘
主執行緒正在處理其他事情(500ms) (執行緒:1)
主執行緒正在處理其他事情(1000ms) (執行緒:1)
主執行緒正在處理其他事情(1500ms) (執行緒:1)
主執行緒正在處理其他事情(2000ms) (執行緒:1)
主執行緒正在處理其他事情(2500ms) (執行緒:1)
主執行緒正在處理其他事情(3000ms) (執行緒:1)
主執行緒正在處理其他事情(3500ms) (執行緒:1)
主執行緒正在處理其他事情(4000ms) (執行緒:1)
主執行緒正在處理其他事情(4500ms) (執行緒:1)
主執行緒正在處理其他事情(5000ms) (執行緒:1)
Press any key for continuing...
  [After] 呼叫 MyMethodAsync().Wait(); 後的執行緒:3
```
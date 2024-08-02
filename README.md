# ASP.NET await �P wait ���t��

��b C# �i��D�P�B (Asynchronous) �u�@�{���]�p���ɭԡA�q�`�|�ݨ즳��ؿ�ܨӵ��ԫD�P�B�u�@�������A�Ĥ@�شN�O�ϥ� Task ���O�Ҵ��Ѫ� Wait() ��k�A�t�~�@�شN�O�ϥ� C# 5.0 ����Ҵ��Ѫ� await ����r�C

��z�Q�n�b�{���X���ϥ� await ����r�A�o�Ӥ�k�����[�W async �׹����A�åB���k�[�W�F async �׹�������A�o�Ӥ�k���^�ǭȶȯ���� Task / Task / void �o�T�̨䤤���@�A�Y�ŧi�F��L���^�ǫ��O�A�N�|�y���sĶ�ɭԲ��Ϳ��~�T���F�Ҧp�A��ŧi�F `static async int My() { }` �o�˪���k�A�N�|�q�sĶ���o��o�˪����~�T���G `���~ CS1983 �D�P�B��k���Ǧ^���������� void�BTask �� Task<T>`

�����A���Ӭd�� await / wait �b�C���r��W���w�q�A���שw�q�O�ƻ�A����W�i�H½Ķ�������ݩΪ̵��ԡA���A�q�o�˪��w�q��r���A�����z�Ѩ�b C# ���o��ӥΪk���t��

[wait](https://dictionary.cambridge.org/zht/����/�^�y/wait)

```
to allow time to go by, especially while staying in one place without doing very much, until someone comes, until something that you are expecting happens or until you can do something
```

[wait](https://dictionary.cambridge.org/zht/����/�^�y/wait)

```
to wait for or be waiting for something
```

�{�b�A���g�@�Ӵ��սd�Ҥp�{��(�p�U�ҥ�)�A�ӤF�� Wait() ��k���B�@�Ҧ��P�S�ʡF��{������� Wait() ��k����A��e��������N�i�J����ꪬ�A�A���O�A�o�˷|���ƻ���D�O�H�]�� Wait() ��k�ݭn���ݫD�P�B�u�@�����槹���A�Y�D�P�B�u�@�S�����槹���A�^���̲װ��浲�G�e�A�o�өI�s Wait() ��k��������N�L�k�����L�{���X�A�]���L�ݭn���_���F�ѡA�D�P�B�u�@���槹���F�S�C

�o�Ӵ��յ{�������|���� DoWait() �o�Ӥ�k�A�b�o�Ӥ�k���A�N�|������ Before() ��k�A��ܥX�@�q�T����r�A���۴N�|�ϥ� Wait() ��k�ӵ��ԫD�P�B�u�@�������A�o�̬O�ϥ� `MyMethodAsync().Wait()` �o�ӱԭz�A�b�o�ӱԭz���A�����|���� MyMethodAsync() ��k�A�Ӧb�o�� MyMethodAsync() ��k���A�����^�ǤF�@�� Task.Run �Ҳ��ͪ��D�P�B�u�@�� Task ����C

�{�b�A�]�� MyMethodAsync() ��k�^�ǤF�@�� Task ����A�]���A�N�i�H�ϥ� Wait() ��k�ӵ��Գo�ӫD�P�B�u�@�������C

�b Main ��k���A�ݨ��I�s���� DoWait() ��k����A�N�|�C�j 0.5 ���ɶ��A��ܤ@�ӰT���C

```csharp
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine($"�D������}�l����A" +
            $"ID={Thread.CurrentThread.ManagedThreadId}");
        Console.WriteLine("�I�s DoWait ��k");
        DoWait();
        for (int i = 0; i < 10; i++)
        {
            Thread.Sleep(500);
            Console.WriteLine($"�D��������b�B�z��L�Ʊ�({(i + 1) * 500}ms) " +
            $"(�����:{Thread.CurrentThread.ManagedThreadId})");
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
        Console.WriteLine($"  [Before] �I�s MyMethodAsync().Wait(); �e��" +
            $"�����:{Thread.CurrentThread.ManagedThreadId}");
    }
    private static void After()
    {
        Console.WriteLine($"  [After] �I�s MyMethodAsync().Wait(); �᪺" +
            $"�����:{Thread.CurrentThread.ManagedThreadId}");
    }
    static Task MyMethodAsync()
    {
        Console.WriteLine($"�i�J�� MyMethodAsync �e�A" +
            $"�ҨϥΪ������ :{Thread.CurrentThread.ManagedThreadId}");
        return Task.Run(() =>
        {
            Console.WriteLine($"MyMethodAsync �}�l�i��D�P�B�u�@�A" +
                $"�ҨϥΪ������ :{Thread.CurrentThread.ManagedThreadId}");
            Console.WriteLine("�ݭn��O 7 ����");
            Thread.Sleep(7000);
        });
    }
}
```

���U���o�Ӵ��սd�ҵ{�������浲�G�A��h�W�A�� �I�s MyMethodAsync().Wait() �ԭz���椧��A�t�~�@�ӭI��������N�w�g�æ����F�A�ӭn���������� MyMethodAsync().Wait() ���᪺�{���X�A�ߤ@�����󨺴N�O�n����o�ӭI�������(�]�N�O�]�p���D�P�B�u�@)���槹����A�~����~���������U�h�C

�]���A�N�|�ݨ�A�u���D�P�B�u�@�����{���X�A�ϥΪ��O ����� 3 �Ӱ���A�Ө�L���{���X���|�b�����1�U�Ӱ���C

```
�D������}�l����AID=1
�I�s DoWait ��k
  [Before] �I�s MyMethodAsync().Wait(); �e�������:1
�i�J�� MyMethodAsync �e�A�ҨϥΪ������ :1
MyMethodAsync �}�l�i��D�P�B�u�@�A�ҨϥΪ������ :3
�ݭn��O 7 ����
  [After] �I�s MyMethodAsync().Wait(); �᪺�����:1
�D��������b�B�z��L�Ʊ�(500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(1000ms) (�����:1)
�D��������b�B�z��L�Ʊ�(1500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(2000ms) (�����:1)
�D��������b�B�z��L�Ʊ�(2500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(3000ms) (�����:1)
�D��������b�B�z��L�Ʊ�(3500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(4000ms) (�����:1)
�D��������b�B�z��L�Ʊ�(4500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(5000ms) (�����:1)
Press any key for continuing...
```

�n���A�W�z�����սd�Ҥp�{���ä������A�u�ݭn�H�P�B�{���X�]�p���רӲz�ѡA�N������D��ӵ{�����B�@�y�{�A�{�b�A�n�ӤF�� await ����r���B�@�覡�A�òz�ѻP Wait() ��k���󤣦P�C

�{�b�A�b Main ��k���A�N�|���@�� DoAwait() ��k�A�ҥH�A��{���X�����o�ӱԭz���ɭԡA�N�|�i�J�� DoAwait() ��k���C�b���k���A�N�|�ϥ� await ����r�ӵ��� MyMethodAsync() �D�P�B�u�@�������A�ϥΪ��O�o�� C# �ԭz `await MyMethodAsync();`�C

awiat ����r�P Wait() ��k�̤j���t���I�N�b��A��ϥΫ�̨ӵ��ݤ@�ӫD�P�B�u�@�A�I�s�ݪ�������]���n���D�D�P�B�u�@�O�_�w�g�����F�A�ҥH�A�|�i�J����ꪬ�A�U�A����a��o�D�P�B�u�@�w�g�����F�F�ӷ�ϥ� await ����r�A��n���ԫD�P�B�u�@���槹�������A�ɭԡA�N�|�ߧY retun �^��I�s�o�Ӥ�k (�O�o�� DoAwait ��k) ���a��A�]�N�O�|�^�� Main ��k���I�s DoAwait ��k���I�A�`�N�A���ɡA�D������O�S���i�J����ꪺ���A�A�]���A�D������N�|�~�����I�s DoAwait() ���᪺�ԭz�A�]�N�O�|�C�j 0.5 ���ɶ��A��ܤ@�ӰT���C

��D������C�j 0.5 ����ܤ@�ӰT���A�åB�� 10 ���A�]�N�O���A�b 5 �������A�N�|��ܥX�Q�ӰT����r�C�N�b���ɡA�I��������o�٦b�������D�P�B���u�@�A�o�ӫD�P�B�u�@�N�|�ݭn 7 �������ɶ��~��������C

�]���A�� 10 �� Main ��k����ܪ��T����r��ܧ�������(�ϥΪ������1�Ӱ���)�A�D�P�B�u�@�O�٨S���������F�ӷ�D�P�B�u�@��������A await MyMethodAsync() �ԭz���᪺�{���X�N�|�n�~��Ӱ���A���ɡA�N�|�I�s After() ��k�C

�{�b�A�ݨ�t�~�@�ӻP�I�s Wait() ��k�����P�I�A��I�s Wait() ��k����A�åB�D�P�B�u�@���槹������A�I�s After() ��k�A��b�O�ϥ� �����1 �o�Ӱ�������~�����F�Ӧb�ϥ� await ����r���ɭԡA�b await ����r���᪺�{���X�n�~�����A�b���O�n�I�s After() ��k�A�o�O�o��F�O�ϥ� �����3 ���~����� await ���᪺�{���X�C

```csharp
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine($"�D������}�l����A" +
            $"ID={Thread.CurrentThread.ManagedThreadId}");
        Console.WriteLine("�I�s DoAwait ��k");
        DoAwait();
        for (int i = 0; i < 10; i++)
        {
            Thread.Sleep(500);
            Console.WriteLine($"�D��������b�B�z��L�Ʊ�({(i + 1) * 500}ms) " +
            $"(�����:{Thread.CurrentThread.ManagedThreadId})");
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
        Console.WriteLine($"  [Before] �I�s MyMethodAsync().Wait(); �e��" +
            $"�����:{Thread.CurrentThread.ManagedThreadId}");
    }

    private static void After()
    {
        Console.WriteLine($"  [After] �I�s MyMethodAsync().Wait(); �᪺" +
            $"�����:{Thread.CurrentThread.ManagedThreadId}");
    }

    static Task MyMethodAsync()
    {
        Console.WriteLine($"�i�J�� MyMethodAsync �e�A" +
            $"�ҨϥΪ������ :{Thread.CurrentThread.ManagedThreadId}");
        return Task.Run(() =>
        {
            Console.WriteLine($"MyMethodAsync �}�l�i��D�P�B�u�@�A" +
                $"�ҨϥΪ������ :{Thread.CurrentThread.ManagedThreadId}");
            Console.WriteLine("�ݭn��O 7 ����");
            Thread.Sleep(7000);
        });
    }
}
```



```
�D������}�l����AID=1
�I�s DoAwait ��k
  [Before] �I�s MyMethodAsync().Wait(); �e�������:1
�i�J�� MyMethodAsync �e�A�ҨϥΪ������ :1
MyMethodAsync �}�l�i��D�P�B�u�@�A�ҨϥΪ������ :3
�ݭn��O 7 ����
�D��������b�B�z��L�Ʊ�(500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(1000ms) (�����:1)
�D��������b�B�z��L�Ʊ�(1500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(2000ms) (�����:1)
�D��������b�B�z��L�Ʊ�(2500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(3000ms) (�����:1)
�D��������b�B�z��L�Ʊ�(3500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(4000ms) (�����:1)
�D��������b�B�z��L�Ʊ�(4500ms) (�����:1)
�D��������b�B�z��L�Ʊ�(5000ms) (�����:1)
Press any key for continuing...
  [After] �I�s MyMethodAsync().Wait(); �᪺�����:3
```
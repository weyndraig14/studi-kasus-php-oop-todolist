# APLIKASI TODOLIST PHP OOP

## POINT UTAMA

### 1. Clean Architecture

- Diagram Clean Architecture

  ![clean_architec](image/clean_architec_diagram.png)

---

### 2. Entity

- kode program Entity

  ```PHP
  namespace Entity {

      class Todolist
      {

          private string $todo;

          public function __construct(string $todo = "")
          {
              $this->todo = $todo;
          }

          public function getTodo(): string
          {
              return $this->todo;
          }

          public function setTodo(string $todo): void
          {
              $this->todo = $todo;
          }

      }

  }
  ```

---

### 3. repository

- Repository digunakan untuk memanipulasi data, seperti menambah, menampilkan dan menghapus data.

  ```PHP
  namespace Repository {

  use Entity\Todolist;

  interface TodolistRepository
  {

      function save(Todolist $todolist): void;

      function remove(int $number): bool;

      function findAll(): array;

  }
  }
  ```

---

### 4. Service

- Buat `action` apa saja yang dibutuhkan di aplikasi.

  ```PHP
  namespace Service {

  use Entity\Todolist;
  use Repository\TodolistRepository;

  interface TodolistService
  {

      function showTodolist(): void;

      function addTodolist(string $todo): void;

      function removeTodolist(int $number): void;

  }
  }
  ```

---

### 5. View

- Digunakan untuk menerima input dari user.

  ```PHP
  namespace View {

      class showTodoList{

          function showTodoList(): void
      }

      class addTodoList{

          function showTodoList(): void
      }

      class removeTodoList{

          function showTodoList(): void
      }
  }
  ```

---

### 6. Implementasi Menampilkan | Repository & Service

- Menampilkan todoList Repository

  ```PHP
  class TodolistRepositoryImpl implements TodolistRepository {

      public array $todolist = array();

      function save(Todolist $todolist): void
      {

      }

      function remove(int $number): bool
      {

      }

      function findAll(): array
      {
          return $this->todolist;
      }
  }
  ```

- Menampilkan todoList Service

  ```PHP
   class TodolistServiceImpl implements TodolistService
  {

      private TodolistRepository $todolistRepository;

      public function __construct(TodolistRepository $todolistRepository)
      {
          $this->todolistRepository = $todolistRepository;
      }

      function showTodolist(): void
      {
          echo "TODOLIST" . PHP_EOL;
          $todolist = $this->todolistRepository->findAll();
          foreach ($todolist as $number => $value) {
              echo "$number. " . $value->getTodo() . PHP_EOL;
          }
      }

      function addTodolist(string $todo): void
      {

      }

      function removeTodolist(int $number): void
      {

      }
  }
  ```

- Test menampilkan todolist Repository & Service

  ```PHP
    use Entity\Todolist;
    use Service\TodolistServiceImpl;
    use Repository\TodolistRepositoryImpl;

    function testShowTodolist(): void
    {
        $todolistRepository = new TodolistRepositoryImpl();
        $todolistRepository->todolist[1] = new Todolist("Belajar PHP");
        $todolistRepository->todolist[2] = new Todolist("Belajar PHP OOP");
        $todolistRepository->todolist[3] = new Todolist("Belajar PHP Database");

        $todolistService = new TodolistServiceImpl($todolistRepository);

        $todolistService->showTodolist();
    }

    testShowTodoList();
  ```

---

### 7. Implementasi Menambah | Repository & Service

- Menambah todolist Repository

  ```PHP
  class TodolistRepositoryImpl implements TodolistRepository {

      public array $todolist = array();

      function save(Todolist $todolist): void
      {
          $number = sizeof($this->todolist) + 1;
          $this->todolist[$number] = $todolist;
      }

      function remove(int $number): bool
      {

      }

      function findAll(): array
      {
          return $this->todolist;
      }
  }
  ```

- Menambah todolist Service

  ```PHP
  class TodolistServiceImpl implements TodolistService{

  function addTodolist(string $todo): void
      {
          $todolist = new Todolist($todo);
          $this->todolistRepository->save($todolist);
          echo "SUKSES MENAMBAH TODOLIST" . PHP_EOL;
      }
  }
  ```

- Test menambah todolist Repository & Service

  ```PHP
  use Entity\Todolist;
  use Service\TodolistServiceImpl;
  use Repository\TodolistRepositoryImpl;

  function testAddTodolist(): void
    {
  $todolistRepository = new TodolistRepositoryImpl();

  $todolistService = new TodolistServiceImpl($todolistRepository);
  $todolistService->addTodolist("Belajar PHP");
  $todolistService->addTodolist("Belajar PHP OOP");
  $todolistService->addTodolist("Belajar PHP Database");

  $todolistService->showTodolist();
    }

  testAddTodoList();
  ```

---

### 8. Implementasi Menghapus | Repository & Service

- Menghapus todoList Repository

  ```PHP
  class TodolistRepositoryImpl implements TodolistRepository {
      function remove(int $number): bool
      {
          if ($number > sizeof($this->todolist)) {
              return false;
          }

          for ($i = $number; $i < sizeof($this->todolist); $i++) {
              $this->todolist[$i] = $this->todolist[$i + 1];
          }

          unset($this->todolist[sizeof($this->todolist)]);

          return true;
      }
  }
  ```

- Menghapus todolist Service

  ```PHP
  class TodolistServiceImpl implements TodolistService
  {
       function removeTodolist(int $number): void
      {
          if ($this->todolistRepository->remove($number)) {
              echo "SUKSES MENGHAPUS TODOLIST" . PHP_EOL;
          } else {
              echo "GAGAL MENGHAPUS TODOLIST" . PHP_EOL;
          }
      }
  }
  ```

- Test menghapus todolist Repository & Service

  ```PHP
  function testRemoveTodolist(): void
  {
      $todolistRepository = new TodolistRepositoryImpl();

      $todolistService = new TodolistServiceImpl($todolistRepository);
      $todolistService->addTodolist("Belajar PHP");
      $todolistService->addTodolist("Belajar PHP OOP");
      $todolistService->addTodolist("Belajar PHP Database");

      $todolistService->showTodolist();

      $todolistService->removeTodolist(1);
      $todolistService->showTodolist();

      $todolistService->removeTodolist(4);
      $todolistService->showTodolist();
  }

  testRemoveTodolist();
  ```

---

### 9. Implementasi View Menambah Todolist

- Buat _class_ input

  ```PHP
  namespace Helper {

  class InputHelper
  {

      static function input(string $info): string
      {
          echo "$info : ";
          $result = fgets(STDIN);
          return trim($result);
      }

  }

  }
  ```

- View menampilkan todoList

  ```PHP
  namespace View {

  use Service\TodolistService;
  use Helper\InputHelper;

  class TodolistView
  {

      private TodolistService $todolistService;

      public function __construct(TodolistService $todolistService)
      {
          $this->todolistService = $todolistService;
      }

      function showTodolist(): void
      {
          while (true) {
              $this->todolistService->showTodolist();

              echo "MENU" . PHP_EOL;
              echo "1. Tambah Todo" . PHP_EOL;
              echo "2. Hapus Todo" . PHP_EOL;
              echo "x. Keluar" . PHP_EOL;

              $pilihan = InputHelper::input("Pilih");

              if ($pilihan == "1") {
                  $this->addTodolist();
              } else if ($pilihan == "2") {
                  $this->removeTodolist();
              } else if ($pilihan == "x") {
                  break;
              } else {
                  echo "Pilihan tidak dimengerti" . PHP_EOL;
              }
          }

          echo "Sampai Jumpa Lagi" . PHP_EOL;
      }
  }
  }
  ```

- Test View menampilkan todoList

  ```PHP
  function testViewShowTodolist(): void
  {

      $todolistRepository = new TodolistRepositoryImpl();
      $todolistService = new TodolistServiceImpl($todolistRepository);
      $todolistView = new TodolistView($todolistService);

      $todolistService->addTodolist("Belajar PHP");
      $todolistService->addTodolist("Belajar PHP OOP");
      $todolistService->addTodolist("Belajar PHP Database");

      $todolistView->showTodolist();
  }

  testViewTodoList();
  ```

### 10. Implementasi View Menambah Todolist

- View menambah todoLits

  ```PHP
  function addTodolist(): void
      {
          echo "MENAMBAH TODO" . PHP_EOL;

          $todo = InputHelper::input("Todo (x untuk batal)");

          if ($todo == "x") {
              echo "Batal menambah todo" . PHP_EOL;
          } else {
              $this->todolistService->addTodolist($todo);
          }
      }
  ```

- Test View menambah todolist

  ```PHP
  function testViewAddTodolist(): void
  {

      $todolistRepository = new TodolistRepositoryImpl();
      $todolistService = new TodolistServiceImpl($todolistRepository);
      $todolistView = new TodolistView($todolistService);

      $todolistService->addTodolist("Belajar PHP");
      $todolistService->addTodolist("Belajar PHP OOP");
      $todolistService->addTodolist("Belajar PHP Database");

      $todolistService->showTodolist();

      $todolistView->addTodolist();

      $todolistService->showTodolist();
  }

  testViewAddTodoList();
  ```

---

### 11. Implementasi View Menghapus TodoList

- View mengahapus todolist

  ```PHP
   function removeTodolist(): void
      {
          echo "MENGHAPUS TODO" . PHP_EOL;

          $pilihan = InputHelper::input("Nomor (x untuk batalkan)");

          if ($pilihan == "x") {
              echo "Batal menghapus todo" . PHP_EOL;
          } else {
              $this->todolistService->removeTodolist($pilihan);
          }
      }
  ```

- Test menghapus todolist

  ```PHP
  function testViewRemoveTodolist(): void
  {

      $todolistRepository = new TodolistRepositoryImpl();
      $todolistService = new TodolistServiceImpl($todolistRepository);
      $todolistView = new TodolistView($todolistService);

      $todolistService->addTodolist("Belajar PHP");
      $todolistService->addTodolist("Belajar PHP OOP");
      $todolistService->addTodolist("Belajar PHP Database");

      $todolistService->showTodolist();

      $todolistView->removeTodolist();
  }

  testViewRemoveTodoList();
  ```

---

### 12. test Keseluruhan

- Require semua directory yang diperlukan

  ```PHP
  require_once __DIR__ . '/Entity/Todolist.php';
  require_once __DIR__ . '/Helper/InputHelper.php';
  require_once __DIR__ . '/Repository/TodolistRepository.php';
  require_once __DIR__ . '/Service/TodolistService.php';
  require_once __DIR__ . '/View/TodolistView.php';

  use Repository\TodolistRepositoryImpl;
  use Service\TodolistServiceImpl;
  use View\TodolistView;

  echo "Aplikasi Todolist" . PHP_EOL;

  $todolistRepository = new TodolistRepositoryImpl();
  $todolistService = new TodolistServiceImpl($todolistRepository);
  $todolistView = new TodolistView($todolistService);

  $todolistView->showTodolist();
  ```

---


## Kesimpulan

- Membuat aplikasi sederhana seperti TodoList ini menggunakan PHP OPP mempermudah dalam management aplikasinya.

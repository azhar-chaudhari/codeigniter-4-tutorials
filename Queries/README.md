# codeigniter-4 Model,Sql queries 
CodeIgniter gives you access to a Query Builder class. This pattern allows information to be retrieved, inserted, and updated in your database with minimal scripting. In some cases, only one or two lines of code are necessary to perform a database action. CodeIgniter does not require that each database table be its own class file. It instead provides a more simplified interface.
 
## Controller (app\Controllers\TaskController.php)


### Select a record (single row)
Select record using primary key id (primary key defined in model app/Models/TaskModel - protected $primaryKey = 'id';)

```php

<?php

namespace App\Controllers;

use App\Controllers\BaseController;
use CodeIgniter\HTTP\IncomingRequest;
 
class TaskController extends BaseController
{
    public function edit($id)
    {
        $taskModel = new TaskModel();
        $data['task'] = $taskModel->find($id);

        return view('tasks/edit', $data);
    }
}

``` 
### Insert a record (single row)

```php
    public function store()
    {
        $taskModel = new TaskModel();

        $data = [
            'title' => $this->request->getPost('title'),
            'description' => $this->request->getPost('description'),
            'due_date' => $this->request->getPost('due_date'),
            'priority' => $this->request->getPost('priority'),
            'created_date' => date('Y-m-d H:i:s'),
        ];

        $taskModel->insert($data);

        return redirect()->to('/tasks');
    }
 ``` 

### Update a record (single row)

Update record using primary key id (primary key defined in model app/Models/TaskModel - protected $primaryKey = 'id';)

```php
    public function update($id)
    {
        $taskModel = new TaskModel();

        $data = [
            'title' => $this->request->getPost('title'),
            'description' => $this->request->getPost('description'),
            'due_date' => $this->request->getPost('due_date'),
            'priority' => $this->request->getPost('priority'),
            'created_date' => date('Y-m-d H:i:s'), 
        ];

        $taskModel->update($id, $data);

        return redirect()->to('/tasks');
    }
``` 
### Delete a record (single row)
Delete record using primary key id (primary key defined in model app/Models/TaskModel - protected $primaryKey = 'id';)

```php
    public function delete($id)
    {
        $taskModel = new TaskModel();
        $taskModel->delete($id);

        return redirect()->to('/tasks');
    }
``` 

### Select a first record using condition  (single row)
Select record using where

```php
public function last_task()
{
    $taskModel = new TaskModel();
    $data['task'] = $taskModel->orderBy('created_date', 'DESC')->first();

    return view('tasks/last_task', $data);
}
```
### Select multiple record using condition  (multiple rows)
Select records using where (between current date to next 7 days)

```php
    public function due_task_in_week()
    {
        $taskModel = new TaskModel();
        $startDate = date('Y-m-d');
        $endDate = date('Y-m-d', strtotime('+7 days'));
        $data['task'] = $taskModel->where('due_date >=', $startDate)
                               ->where('due_date <=', $endDate)
                               ->findAll();

        return view('tasks/due_task_in_week', $data);
    }
```
### Select top 5 records using where (multiple row)
Select top 5 records Pagination (limit)

```php
public function top5Tasks()
{
    $taskModel = new TaskModel();

    // Fetch the top 5 tasks based on a specific criterion (e.g., due date)
    $data['tasks'] = $taskModel->orderBy('due_date', 'ASC')->limit(5)->findAll();

    return view('tasks/top5_tasks', $data);
}
```
### Select count of records by group (multiple row)
Select count using priority (groupby)

```php
public function countTasksByPriority()
{
    $taskModel = new TaskModel();

    // Count tasks grouped by priority
    $data['priorityCounts'] = $taskModel->groupBy('priority')->countAllResults();

    return view('tasks/priority_counts', $data);
}
```


### Delete a record using where (single row)
Delete record using condition provided 

```php
    public function delete_by_priority()
    {
        $taskModel = new TaskModel();
        $priority = $this->request->getPost('priority');

        $taskModel->where('priority',$priority)->delete();

        return redirect()->to('/tasks');
    }
``` 
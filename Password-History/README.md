# codeigniter-4 Password-History
Storing a history of user passwords to prevent the reuse of recent passwords
 
## Database table
```sql
CREATE TABLE password_history (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    created_at DATETIME DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id)
);

```
## Model (app\Models\PasswordHistoryModel.php)


```php

namespace App\Models;

use CodeIgniter\Model;

class PasswordHistoryModel extends Model
{
    protected $table = 'password_history';
    protected $primaryKey = 'id';
    protected $allowedFields = ['user_id', 'password_hash', 'created_at'];
}

``` 
## Model (app\Controller\AuthController.php)
```php
<?php

namespace App\Controllers;

use App\Models\PasswordHistoryModel;
use CodeIgniter\Controller;

class AuthController extends Controller
{
    public function changePassword()
    {
        $userId = session()->get('user_id');
        $newPassword = $this->request->getPost('new_password');

        // Load the password history model
        $passwordHistoryModel = new PasswordHistoryModel();

        // Fetch the user's recent password hashes
        $recentPasswords = $passwordHistoryModel->where('user_id', $userId)
                                               ->orderBy('created_at', 'DESC')
                                               ->findAll(5); // Check last 5 passwords

        // Hash the new password
        $newPasswordHash = password_hash($newPassword, PASSWORD_BCRYPT);

        // Check if the new password hash matches any of the recent ones
        foreach ($recentPasswords as $history) {
            if (password_verify($newPassword, $history['password_hash'])) {
                return redirect()->back()->with('error', 'You cannot reuse your recent passwords.');
            }
        }

        // Save the new password hash in the history table
        $passwordHistoryModel->insert([
            'user_id' => $userId,
            'password_hash' => $newPasswordHash,
        ]);

        // Update the user's current password (assuming you have a UserModel)
        $userModel = new \App\Models\UserModel();
        $userModel->update($userId, ['password' => $newPasswordHash]);

        return redirect()->to('/profile')->with('success', 'Password changed successfully.');
    }
}
``` 
## View

```php
<form action="<?= site_url('auth/changePassword') ?>" method="post">
    <input type="password" name="new_password" placeholder="New Password" required>
    <button type="submit">Change Password</button>
</form>

```
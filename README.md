# evasion

```
import tkinter as tk
from tkinter import filedialog, messagebox
from cryptography.fernet import Fernet
import threading
import time
import os
import sys
import subprocess

# Generate encryption key and cipher suite
key = Fernet.generate_key()
cipher_suite = Fernet(key)

def encrypt_file(filename):
    """Encrypt the contents of the given file and save it back to the file."""
    with open(filename, 'rb') as f:
        plaintext = f.read()
    encrypted_data = cipher_suite.encrypt(plaintext)
    
    with open(filename, 'wb') as f:
        f.write(encrypted_data)
        
    print("File after encrpytion")
    print(encrypted_data)
    
    return filename

def increase_file_size(filename, target_size_mb):
    """Increase the size of the given file to the target size by appending additional bytes."""
    target_size = target_size_mb * 1024 * 1024
    with open(filename, 'ab') as f:
        current_size = os.path.getsize(filename)
        additional_size = target_size - current_size
        additional_data = b'\x00' * additional_size
        f.write(additional_data)
    
    return filename

def decrypt_file(filename):
    """Decrypt the contents of the given file and return the decrypted data."""
    with open(filename, 'rb') as f:
        encrypted_data = f.read()
    
    decrypted_data = cipher_suite.decrypt(encrypted_data)
    return decrypted_data

def execute_program(decrypted_data, original_filename):
    """Execute the decrypted Python program file."""
    # Save the decrypted data to the original file
    with open(original_filename, 'wb') as f:
        f.write(decrypted_data)

    # Execute the Python file using its full path
    print("Executing the python file")
    subprocess.run(['python', original_filename])

def delayed_execution(filename):
    """Delay execution by 10 seconds, print the file size, then decrypt and execute the program."""

    for i in range(10):
        print(f"Time passed: {i+1} seconds")
        time.sleep(1)  # Simulate delay with intermediate prints

    print("File contents after decryption:")
    decrypted_data = decrypt_file(filename)
    # Print the decrypted data as text (assuming it's a Python file)
    print(decrypted_data.decode('utf-8'))

    execute_program(decrypted_data, filename)
    sys.exit()

def select_program_file():
    """Prompt the user to select a Python file and process it."""
    filename = filedialog.askopenfilename(filetypes=[("Python Files", "*.py")])

    if filename:
        encrypted_filename = encrypt_file(filename)
        increased_filename = increase_file_size(encrypted_filename, 101)

        # Start the delayed execution in a separate thread to keep the GUI responsive
        thread = threading.Thread(target=delayed_execution, args=(increased_filename,))
        thread.start()
        root.destroy()

# Set up the GUI
root = tk.Tk()
root.title("Program Encryptor")

select_button = tk.Button(root, text="Select Program File", command=select_program_file)
select_button.pack(pady=20)

root.mainloop()

```

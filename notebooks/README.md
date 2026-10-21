# notebooks

Jupyter notebooks are a comfortable place to try one image step, look at the picture, and then try the next step.

From the project folder, with the virtual environment activated (see [the setup guide](../docs/setup.md)):

```bash
jupyter notebook
```

Open the link it prints. Inside the Ubuntu virtual machine, that link works in the Ubuntu web browser.

Notebooks are scratch space. Code you want to keep using belongs in [src](../src/) later. Jupyter's checkpoint folders are ignored by git. A notebook you want to remember should be saved and committed on purpose, and it is fine to keep early notebooks out of git until they contain something you might want again.

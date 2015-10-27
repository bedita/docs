# BEdita documentation

This is the repository of the BEdita documentation you can see at https://bedita.readthedocs.org

# Build the documentation manually

The documentation is built using [Sphinx](http://sphinx-doc.org/).

In order to build the documentation you need [pip](https://pip.pypa.io) (the tool for installing Python packages).
Recent versions of Pyhton (>= 2.7.9) already include [pip](https://pip.pypa.io).
If it missing you can easily [install](https://pip.pypa.io/en/stable/installing/#install-pip) it.

Once [pip](https://pip.pypa.io) is installed, you have to move to the directory  where you cloned this project

```bash
cd /dir/bedita/docs
```

install the project dependencies (which may require administrator access)

```bash
pip install -r requirements.txt
```

compile the documentation

```bash
make html
```

That's it.

You would see a new `build` directory in the root of the project.
Open `build/html/index.html` with a browser to see the compiled documentation.

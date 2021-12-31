# doc_demo

```shell script
conda create -n docs python=3.7 -y
conda activate docs
pip install sphinx_rtd_theme
pip install -r requirements/docs.txt
pip install -r requirements/readthedocs.txt
cd docs/en
make html
cd ../zh_cn
make html
```

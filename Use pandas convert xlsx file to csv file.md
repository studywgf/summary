---
title: Use pandas convert xlsx file to csv file
tags: python，pandas，excel，csv
---

````markdown
import pandas as pd

'''
Function: Convert xlsx file to scv file
Arguments:
  xlsx_path: Origin xlsx file path
  csc_path:  Destination csv file path
Return: 
  None
'''
def xlsx_to_csv(xlsx_path: str, csv_path: str) -> None:
    data_xls = pd.read_excel(xlsx_path, index_col=0)
    data_xls.to_csv(csv_path, encoding='utf-8')
````

# barcode-python
Como gerar códigos de barras a partir de um fuckup em python

Salvar o doc “gerador.py” abaixo no Desktop ou em alguma pasta de preferência

```
import csv
import os
import requests
import shutil
import urllib


"""
Docs: http://labelary.com/service.html
"""
url = 'http://api.labelary.com/v1/printers/8dpmm/labels/3x1.5/0/'


data_source_file = 'data.csv'
output_directory = 'output'


if not os.path.exists(output_directory):
    os.makedirs(output_directory)


with open(data_source_file, 'rb') as csvfile:
    rows = csv.reader(csvfile, delimiter=';')
    for identifier, barcode in rows:
        print 'identifier: {}  -- barcode: {}'.format(identifier, barcode)


        zpl = ''.join(['^XA^BY4,5,200^FO010,10^BC^FD', barcode, '^FS^XZ'])
        files = {'file': zpl}
        response = requests.post(url, files=files, stream=True)


        if response.status_code == 200:
            response.raw.decode_content = True


            filename = ''.join([identifier, '.png'])
            with open(os.path.join(output_directory, filename), 'wb') as out_file:
                shutil.copyfileobj(response.raw, out_file)
        else:
            print('Error: ' + response.text)
```
Criar um arquivo “data.csv” no Excel e subir as informações dos barcodes como “identifier” e “barcode"
identifier
barcode
01
567835354634
* Rodar o arquivo “gerador.py” no Terminal nessa sequência (para leigo):
    * Rodar o comando “cd + TAB + TAB"
    * Rodar o comando "cd Desktop/"
python gerador.py
* Os arquivos vão ser salvos em uma pasta chamada “output” no Desktop


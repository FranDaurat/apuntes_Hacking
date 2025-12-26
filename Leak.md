-- -
Usuarios1 ---> 35
Usuarios2 ---> 23
afip ---> 895
anses ---> 900
autos ---> 1605
bcra ---> 2110
celulares ---> 698
crm ---> 154 
gestion ---> 174
jubilados ---> 147
marketing ---> 62
subsidios ---> 171
telefonia ---> 275

### Comando:

```bash
proxychains4 wget2 -r --host-directories=off --parent=off -t 500 --retry-on-http-error=500,501,502,503,504,429 --check-certificate=off --max-threads=100 http://xojbjk5saa67n3mxsgpp7eqyrrwk7vcpndz2qaf3byhfsailrljkzxad.onion/USUARIOS1/ && notify.sh "✅ Descarga USUARIOS1 completada con ÉXITO" || notify.sh "❌ Error en descarga de USUARIOS1" 
```

```bash
proxychains4 aria2c --input-file=resources_u1.txt \  
--async-dns=false \  
--max-concurrent-downloads=5 \  
--max-connection-per-server=16 \  
--split=16 \  
--min-split-size=1M \  
--max-tries=0 \  
--retry-wait=5 \  
--save-session=descarga_u1.session \  
--save-session-interval=60 \  
--continue=true \  
--disable-ipv6=true \  
&& notify.sh "✅ Lote descargado" \  
|| notify.sh "❌ Error en lote"
```

**Downloader**
```bash 
#!/bin/bash

while read -r url; do
	proxychains4 aria2c --async-dns=false --max-connection-per-server=16 --split=16 --min-split-size=1M --max-tries=0 --retry-wait=5 --save-session=descarga.session --save-session-interval=60 --continue=true --disable-ipv6=true "$url" && notify.sh "✅ Descarga OK: $URL" || notify.sh "❌ Error descarga: $URL"
done < resources.txt
```

http://173.249.9.91:49581


### Comando para descargar
```bash
aria2c --user-agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36" -x 10 -s 10 --save-session=descarga.session --continue https://161.129.67.66/sudamericadata.tar.gz && notify.sh "✅ Descarga OK" || notify.sh "❌ Error descarga"
```

### Arreglar conectividad
```bash
#!/bin/bash

  

echo "🚑 Restaurando red..."

  

# 1. Borrar rutas basura

echo "🧹 Limpiando rutas..."

sudo ip route flush table main

sudo ip route flush cache

  

# 2. Pedir IP correcta al router (esto arregla el gateway 192.168.0.1)

echo "🔄 Renovando IP (DHCP)..."

sudo dhclient -r enp5s0

sudo dhclient -v enp5s0

  

# 3. Reiniciar Mullvad para que se acomode

echo "🔒 Reiniciando VPN..."

mullvad disconnect

sleep 2

mullvad connect

  

echo "✅ Listo. Prueba con: curl ifconfig.me"
```

mega-get "https://mega.nz/file/zNEGnajB#yd3-KMxXsY1KXP7kHwt7LMMZ55Zqi-m_mYkT5Hz5GDc" && notify.sh "✅ Descarga OK" || notify.sh "❌ Error descarga"


-- -
# Archivos Filtrados Organizados por Categoría

IP Origen: `http://173.249.9.91:49581/`

## 🏦 Anses_DDJJ

http://173.249.9.91:49581/anses_2025/anses_2025_1.txt
http://173.249.9.91:49581/anses_2025/anses_2025_10.txt
http://173.249.9.91:49581/anses06_2025_int.csv
http://173.249.9.91:49581/anses06_2025_int.sql
http://173.249.9.91:49581/anses07_2025_int.csv
http://173.249.9.91:49581/anses07_2025_int.sql
http://173.249.9.91:49581/anses0_2024struct.txt
http://173.249.9.91:49581/anses0struct.txt
http://173.249.9.91:49581/laboral_2024anses1.csv
http://173.249.9.91:49581/laboral_2024anses2.csv
http://173.249.9.91:49581/laboral_2024anses3.csv
http://173.249.9.91:49581/vw_anses2025_unico2.csv
http://173.249.9.91:49581/vw_anses2025_unico_struct.txt
http://173.249.9.91:49581/vw_anses_2025_int_unida.csv
http://173.249.9.91:49581/vw_anses_2025_int_unida_struct.txt
http://173.249.9.91:49581/vwansesstruct.txt
http://173.249.9.91:49581/Anses%20Activos%20ddjj%20Agosto%202024.rar
http://173.249.9.91:49581/Anses%20Activos%20ddjj%20Julio%202024.rar
http://173.249.9.91:49581/ActAnses%20ddjj%20Marzo%202023.rar

## 🗳️ Padrón Fallecido exterior extranjeros

http://173.249.9.91:49581/20230430PADRON.7Z
http://173.249.9.91:49581/20230531PADRON.7Z
http://173.249.9.91:49581/20240831PADRON.7Z 
http://173.249.9.91:49581/20240930PADRON.7Z
http://173.249.9.91:49581/20251031PADRON.7Z
http://173.249.9.91:49581/Padron_202308.rar
http://173.249.9.91:49581/Padron_AFIP_09_2025_docu.csv
http://173.249.9.91:49581/Padron_provisorio2023.csv
http://173.249.9.91:49581/Padron_provisorio2023.rar
http://173.249.9.91:49581/ResultadosElectorales_2023.csv
http://173.249.9.91:49581/electoral_2021_completo.txt
http://173.249.9.91:49581/padfallecidos_202511.mdb
http://173.249.9.91:49581/padfallecidos_202511.rar
http://173.249.9.91:49581/padron_2023_domicilio.csv
http://173.249.9.91:49581/padron_2023_domicilio.sql
http://173.249.9.91:49581/padron_exterior.txt
http://173.249.9.91:49581/padron_extranjeros.txt
http://173.249.9.91:49581/padron_logia_1_20251119_202428.pdf
http://173.249.9.91:49581/padron_logia_2_20251119_203408.pdf

## 🚗 Automotores

http://173.249.9.91:49581/Automotores2023.mdb
http://173.249.9.91:49581/Automotores2024.mdb
http://173.249.9.91:49581/Automotores2024.rar
http://173.249.9.91:49581/Automotores20246.mdb
http://173.249.9.91:49581/Automotores20246.rar
http://173.249.9.91:49581/Automotores202461.mdb
http://173.249.9.91:49581/Automotores2025.mdb
http://173.249.9.91:49581/Automotores2025_3.rar
http://173.249.9.91:49581/Automotores2025_4.rar
http://173.249.9.91:49581/Automotores2025_7.mdb
http://173.249.9.91:49581/Automotores2025_7.rar
http://173.249.9.91:49581/autos.bak
http://173.249.9.91:49581/autos_db_autos.txt
http://173.249.9.91:49581/autos_total.rar
http://173.249.9.91:49581/autosv.bak

## 💰 Economía, AFIP arca & Deudores ddjj

http://173.249.9.91:49581/202407DEUDORES.7Z
http://173.249.9.91:49581/202408DEUDORES.7Z
http://173.249.9.91:49581/202509DEUDORES.7Z
http://173.249.9.91:49581/20251031CHEQUES.7z
http://173.249.9.91:49581/Arca202511.mdb
http://173.249.9.91:49581/Arca202511.rar
http://173.249.9.91:49581/Autonomos202404.rar
http://173.249.9.91:49581/Autonomos202407.rar
http://173.249.9.91:49581/Autonomos202408.rar
http://173.249.9.91:49581/Autonomos202505.rar
http://173.249.9.91:49581/afip.bak
http://173.249.9.91:49581/autonomos.bak
http://173.249.9.91:49581/bcra.bak
http://173.249.9.91:49581/juicios.bak
http://173.249.9.91:49581/subsidios.bak
http://173.249.9.91:49581/subsidios_argenta.csv
http://173.249.9.91:49581/subsidios_ife.csv
http://173.249.9.91:49581/Activos%20DDJJ%20%20Septiembre%202023.rar
http://173.249.9.91:49581/Activos%20Full%20%20DJ%20Agosto%20%202025.rar
http://173.249.9.91:49581/Activos_Full_DDJJ_Octubre_2025.rar
http://173.249.9.91:49581/Nuevos%20Activos%20DJ%20Mayo%202025.txt

## 📞 marketing_celulares

http://173.249.9.91:49581/Contactanos.sql
http://173.249.9.91:49581/Marketing_H_campania1.csv
http://173.249.9.91:49581/Marketing_inferido_campania1.csv
http://173.249.9.91:49581/Marketing_inferido_campania2.csv
http://173.249.9.91:49581/Marketing_procompra_campania1.sql
http://173.249.9.91:49581/Marketing_procompra_ref_campania1.csv
http://173.249.9.91:49581/Marketing_procompra_ref_campania1.sql
http://173.249.9.91:49581/Marketing_recoleccion_campania3.csv
http://173.249.9.91:49581/Marketing_tit_campania3.sql
http://173.249.9.91:49581/celulares.bak
http://173.249.9.91:49581/marketing.bak
http://173.249.9.91:49581/marketing_arg_campania1.csv
http://173.249.9.91:49581/telefonia.bak

## 👥 Socios, Afiliados & Misc

http://173.249.9.91:49581/Socios.bak
http://173.249.9.91:49581/USUARIOS1.bak
http://173.249.9.91:49581/USUARIOS2.bak
http://173.249.9.91:49581/adhesiones.bak
http://173.249.9.91:49581/afiliados_neuquen.xls
http://173.249.9.91:49581/agroservicios.xls
http://173.249.9.91:49581/antares.bak
http://173.249.9.91:49581/avant.bak
http://173.249.9.91:49581/contratistas.bak
http://173.249.9.91:49581/crm.bak
http://173.249.9.91:49581/elecciones.bak
http://173.249.9.91:49581/empresas_seguridad.xls
http://173.249.9.91:49581/gestion_global.csv
http://173.249.9.91:49581/listado%20de%20afiliados%20que%20cumplen%20requisitos%20para%20ingresar%20a%20las%20asambleas%20del%2020sep23.xls
http://173.249.9.91:49581/listado.txt
http://173.249.9.91:49581/listado.xls
http://173.249.9.91:49581/numeros_tierra_del_fuego.txt
http://173.249.9.91:49581/sci.bak
http://173.249.9.91:49581/servicios.bak
http://173.249.9.91:49581/usuarios.xls

## 👥 Jubilados
http://173.249.9.91:49581/jubi_12_2025.rar
http://173.249.9.91:49581/jubilados.bak
http://173.249.9.91:49581/jubilados_Conceptos_full_2512.rar
http://173.249.9.91:49581/jubilados_Titapod_full_2512.rar

## 📂 Directorios

http://173.249.9.91:49581/SEGMENTADOS_100MB/
http://173.249.9.91:49581/anses_2025/
http://173.249.9.91:49581/codigos%20fuente/
http://173.249.9.91:49581/dump/

## 📦 Otros

http://173.249.9.91:49581/24DSF202303.7Z
http://173.249.9.91:49581/24DSF202407.7Z
http://173.249.9.91:49581/Conasulta_mensual_Jorge_12_2025.rar
http://173.249.9.91:49581/Demo082025.xls
http://173.249.9.91:49581/IMPENETRABLE.xls
http://173.249.9.91:49581/Solicitud_alta.sql
http://173.249.9.91:49581/noseusa.rar
http://173.249.9.91:49581/pgmp.rar
http://173.249.9.91:49581/wetransfer_1dsf202303-7z_2023-05-03_1915.zip
http://173.249.9.91:49581/wetransfer_1dsf20230
http://173.249.9.91:49581/laboral_2024_marketing1.csv
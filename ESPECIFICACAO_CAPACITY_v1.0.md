<h1 align="center"> Documentação [ CAPACITY ]  </h1>


## Controle de versão:

<div align="center">

| Emissao | 1 | 2 | 3 |
|:-:|:-:|:-:|:-:|
| Data | 13/11/2026 | - | - |

</div>

<div align="center">

| Rev | Data | Elaborado |
|:-:|:-:|:-:|
| 1 | 13/11/2026 |  Aurelio B. Zanforlin  |

</div>

## Apresentação:

Processo atual de como funciona a geração de relatorios de todos os TGs configurado em SBCs, Balanceadores (SRR) e iBCFs

### a. Server e DataBase e Tabela. 

São utilizados dois servidores. 

<div align="center">

| Server           | Name               | IP                     | Descrição |
|------------------|--------------------|------------------------|-----------|
| Server-Eng_CX    | [ svuxpexcap01 ]   | `10.200.35.226`        | onde fica a maior parte do código e a base de dados dbengcx. |
| Server-EngCX_VTC | [ eng-core-voz ]   | `10.11.138.142`        | onde fica o script de consulta da genband. |

</div>

### b. NEs e Vendors

Para Siptrunk Massivo, temos consultas conforme abaixo:

* Servidor de Gerencia SDM: Todos os SBCs ACME legados na topologia V2.
* Servidor de Gerencia RSM: Dois RMS ditintos um V2 em um V1, para SBCs Q20 e Q21. 
* Servidor de Gerencia EMS: Todos os SBCs Sons SWe.
* Servidor de Gerencia Taishan 2020 Huawei. Todos os SRR, SBCs e iBCFs.

Nota: Em breve teremos um novo SDM, onde teremos SBCs novos ACME, e um novo RAMP, onde tudo da Ribbon será migrado. 

### c.  Topologia de Consultas.

* Colocar a imagem aqui no lugar topologia.png

# Scripts
## 1. Consulta Genband V2


Diretório raiz: `/home/capacidade/SBC/GENBAND/tgrpRel/rest`

- **Step 1:** Gerar o arquivo `sbcDeviceList.xml`, usado o payload `getSBCList.xml`:

````bash
curl -k -X POST "https://10.141.245.77/rsm/ws/prov/mswinfo" \
-H "Content-Type: text/xml; charset=utf-8" \
-d @getSBCList.xml | xmllint --format - > sbcDeviceList.xml
````

Arquivo: `getSBCList.xml`
````xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:con="http://www.nextone.com/ivms/schema/config" xmlns:com="http://www.nextone.com/ivms/schema/common">
   <soapenv:Header>
   </soapenv:Header>
   <soapenv:Body>
      <con:getConfig>
         <com:credential>
            <com:user>soap</com:user>
            <com:password>So@p1234</com:password>
            <!--Optional:-->
            <com:partition>admin</com:partition>
         </com:credential>
         <!--Zero or more repetitions:-->
         <con:mswInfoFilter type="subtree" pageIndex="1" pageSize="100" sequenceNumber="1">
            <!--Optional:-->
                <deviceName xsi:nil="true" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"/>
         </con:mswInfoFilter>
      </con:getConfig>
   </soapenv:Body>
</soapenv:Envelope>
````
Arquivo gerado: `sbcDeviceList.xml`
````xml
<?xml version="1.0"?>
<env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/">
  <env:Header/>
  <env:Body>
    <ns3:reply xmlns:ns2="http://www.nextone.com/ivms/schema/common" xmlns:ns3="http://www.nextone.com/ivms/schema/config">
      <ok>ok</ok>
      <error xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
      <data>
        <mswInfoConfig>
          <deviceName>N/A</deviceName>
          <clusterId>0</clusterId>
          <mswid xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <mswip/>
          <cllicode/>
          <description>Dummy MSW</description>
          <created xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <lastModified xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <currentversion/>
          <otherversion/>
          <snmpversion>v2c</snmpversion>
        </mswInfoConfig>
        <mswInfoConfig>
          <deviceName>sbc10a-cta</deviceName>
          <clusterId>1</clusterId>
          <mswid xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <mswip>10.200.36.78</mswip>
          <cllicode/>
          <description/>
          <created xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <lastModified xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <currentversion>9.3.31.0</currentversion>
          <otherversion/>
          <snmpversion>v2c</snmpversion>
        </mswInfoConfig>
          <deviceName>sbc10a-spo</deviceName>
          <clusterId>2</clusterId>
          <mswid xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <mswip>10.211.32.60</mswip>
          <cllicode/>
          <description/>
          <created xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <lastModified xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <currentversion>9.3.31.0</currentversion>
          <otherversion/>
          <snmpversion>v2c</snmpversion>
        </mswInfoConfig>
        <mswInfoConfig>
          <deviceName>sbc10b-spo</deviceName>
          <clusterId>2</clusterId>
          <mswid xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <mswip>10.211.32.63</mswip>
          <cllicode/>
          <description/>
          <created xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <lastModified xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:nil="true"/>
          <currentversion>9.3.31.0</currentversion>
          <otherversion/>
          <snmpversion>v2c</snmpversion>
        </mswInfoConfig>
      </data>
    </ns3:reply>
  </env:Body>
</env:Envelope>
````

- **Step 2:** Criar o arquivo .csv e deixar ele com linhas únicas: 
````bash
xmlstarlet sel -t -m "/devices/device"   -v "id" -o ","   -v "ip" -o ","   -v "nfName"   -n sbcDeviceList.xml > sbcDeviceList.csv
awk -F',' '!seen[$3]++' sbcDeviceList.csv > sbcDeviceList_unico.csv
rm -f sbcDeviceList.csv
mv sbcDeviceList_unico.csv sbcDeviceList.csv
````

````bash
[root@svuxpexcap01 rest]# cat sbcDeviceList.csv
sbc10a-cta,10.200.36.78
sbc10b-cta,10.200.36.79
sbc10a-spo,10.211.32.60
sbc10b-spo,10.211.32.63
.
.
.
````
|sbc|ip|
|-|-|
sbc10a-cta|10.200.36.78
sbc10b-cta|10.200.36.79
sbc10a-spo|10.211.32.60
sbc10b-spo|10.211.32.63
sbc9a-spo|10.209.160.40
sbc9b-spo|10.209.160.41
sbc1a-cge|10.185.32.15
sbc1b-cge|10.185.32.16
sbc1a-aju|10.185.96.25
sbc1b-aju|10.185.96.26
sbc1a-bnu|10.141.80.14
sbc1b-bnu|10.141.80.15
sbc1a-cpe|10.161.64.44
sbc1b-cpe|10.161.64.45
sbc2a-cas|10.211.160.76
sbc2b-cas|10.211.160.78
sbc1a-csc|10.141.56.11
sbc1b-csc|10.141.56.12
sbc1a-csl|10.151.56.14
sbc1b-csl|10.151.56.15
sbc1a-foz|10.141.64.15
sbc1b-foz|10.141.64.16
sbc1a-fsa|10.171.32.27
sbc1b-fsa|10.171.32.28
sbc1a-gvs|10.131.96.29
sbc1b-gvs|10.131.96.30
sbc1a-jpa|10.185.64.15
sbc1b-jpa|10.185.64.16
sbc1a-mco|10.185.128.28
sbc1b-mco|10.185.128.29
sbc1a-ntl|10.185.160.29
sbc1b-ntl|10.185.160.30
sbc2a-pae|10.151.4.26
sbc2b-pae|10.151.4.27
sbc2a-rce|10.181.0.50
sbc2b-rce|10.181.0.51
sbc3a-rjo|10.221.64.100
sbc3b-rjo|10.221.64.102
sbc3a-sdr|10.171.0.88
sbc3b-sdr|10.171.0.89
sbc2a-bhe|10.131.23.23
sbc2b-bhe|10.131.23.24
sbc2a-bsa|10.161.0.28
sbc2b-bsa|10.161.0.29



Os próximos passos são todos automáticos:

- **Step 2:** Execução do arquivo consultaSBC.sh todo dia as 23h40min:

Linha do arquivo crontab: [ adicionar com o comando `crontab -e`, comandos `'vi'` Teclas `<Win> + <Shift> + <i>` cola a linha aperta `<Esc>` escreve e sai com `:wq!` ]

`40 23 * * * /home/capacidade/SBC/GENBAND/tgrpRel/rest/consultaSBC.sh >> /home/capacidade/SBC/GENBAND/tgrpRel/rest/logDiario.log 2>&1`


Arquivo: `consultaSBC.sh`
````bash
#!/bin/bash

set -u
ARQUIVO="/home/capacidade/SBC/GENBAND/tgrpRel/rest/sbcDeviceList.csv"
URL="https://10.141.245.77/rsm/ws/prov/ep"
PAGE_SIZE=256
# Quantos SBCs em paralelo
SBC_PARALLEL=20
# Quantas páginas simultâneas por SBC
PAGE_PARALLEL=8
mkdir -p /home/capacidade/SBC/GENBAND/tgrpRel/rest/sbcLogs
mkdir -p /tmp/sbc_parallel
process_page() {
    local SBC="$1"
    local PAGE="$2"
    local TMPDIR="/tmp/sbc_parallel/${SBC}"
    mkdir -p "$TMPDIR"
    local REQUEST="${TMPDIR}/request_${PAGE}.xml"
    local RESPONSE="${TMPDIR}/response_${PAGE}.xml"
    local CSVPAGE="${TMPDIR}/page_${PAGE}.csv"
    cat > "$REQUEST" <<EOF
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
                  xmlns:con="http://www.nextone.com/ivms/schema/config"
                  xmlns:com="http://www.nextone.com/ivms/schema/common">
   <soapenv:Header/>
   <soapenv:Body>
      <con:getConfig>
         <com:credential>
            <com:user>soap</com:user>
            <com:password>So@p1234</com:password>
            <com:partition>admin</com:partition>
         </com:credential>
         <con:endpointFilter
             type="subtree"
             pageIndex="${PAGE}"
             pageSize="${PAGE_SIZE}"
             sequenceNumber="1">
            <deviceName>${SBC}</deviceName>
            <serialNumber></serialNumber>
            <UPort></UPort>
         </con:endpointFilter>
      </con:getConfig>
   </soapenv:Body>
</soapenv:Envelope>
EOF
    curl -k -s \
        -X POST \
        -H "Content-Type: text/xml; charset=UTF-8" \
        --data @"$REQUEST" \
        "$URL" \
        -o "$RESPONSE"
    local QTD
    QTD=$(xmlstarlet sel \
        -t \
        -v "count(//sipEndpoint)" \
        "$RESPONSE" 2>/dev/null)
    [ -z "$QTD" ] && QTD=0
    if [ "$QTD" -gt 0 ]; then
        xmlstarlet sel \
          -N env="http://schemas.xmlsoap.org/soap/envelope/" \
          -N ns3="http://www.nextone.com/ivms/schema/config" \
          -t \
          -m "//endpointConfig/sipEndpoint" \
          -v "realmName" -o "," \
          -v "dtgc" -o "," \
          -v "srcEgressTg" -o "," \
          -v "destTrunkGroup" -o "," \
          -v "srcIngressTg" -o "," \
          -v "srcEgressTg" -o "," \
          -v "PaidUserName" -o "," \
          -v "maxCalls" -o "," \
          -v "newTgcIngress" -o "," \
          -v "fmmIngressProfile" -o "," \
          -v "IPAddress" -o "," \
          -v "serialNumber" -o "," \
          -v "UPort" -o "," \
          -v "customerId" \
          -n \
          "$RESPONSE" > "$CSVPAGE"
    else
        > "$CSVPAGE"
    fi
    echo "${PAGE};${QTD}"
}
export URL
export PAGE_SIZE
export -f process_page
process_sbc() {
    local SBC="$1"
    echo "[$SBC] Iniciando"
    local TMPDIR="/tmp/sbc_parallel/${SBC}"
    local CSVFINAL="/home/capacidade/SBC/GENBAND/tgrpRel/rest/sbcLogs/clientes_${SBC}.csv"
    rm -rf "$TMPDIR"
    mkdir -p "$TMPDIR"
    > "$CSVFINAL"
    local START_PAGE=1
    local LAST_PAGE_FOUND=0
    while true
    do
        seq "$START_PAGE" "$((START_PAGE + PAGE_PARALLEL - 1))" \
        | xargs -I{} -P "$PAGE_PARALLEL" \
          bash -c 'process_page "$0" "$1"' "$SBC" {} \
        > "${TMPDIR}/result_block.txt"
        local STOP=0
        while IFS=';' read -r PAGE QTD
        do
            if [ "$QTD" -lt "$PAGE_SIZE" ]; then
                if [ "$LAST_PAGE_FOUND" -eq 0 ] || [ "$PAGE" -lt "$LAST_PAGE_FOUND" ]
                then
                    LAST_PAGE_FOUND="$PAGE"
                fi
                STOP=1
            fi
        done < "${TMPDIR}/result_block.txt"
        if [ "$STOP" -eq 1 ]; then
            break
        fi
        START_PAGE=$((START_PAGE + PAGE_PARALLEL))
    done
    if [ "$LAST_PAGE_FOUND" -eq 0 ]; then
        LAST_PAGE_FOUND=$((START_PAGE + PAGE_PARALLEL - 1))
    fi
    for PAGEFILE in $(ls "${TMPDIR}"/page_*.csv 2>/dev/null | sort -V)
    do
        cat "$PAGEFILE" >> "$CSVFINAL"
    done
    TOTAL=$(wc -l < "$CSVFINAL")
    echo "[$SBC] Finalizado - ${TOTAL} registros"
    rm -rf "$TMPDIR"
}
export PAGE_PARALLEL
export -f process_sbc
cut -d',' -f1 "$ARQUIVO" \
| grep -v '^$' \
| xargs -I{} -P "$SBC_PARALLEL" \
    bash -c 'process_sbc "$@"' _ {}
/home/capacidade/SBC/GENBAND/tgrpRel/rest/sbcLogs/gerarBase.sh
echo
echo "Processamento concluído."
````

Respostas esperadas da execução desse programa:
````groovy
cat logDiario.log
Processamento concluído.
[sbc10a-cta] Iniciando
[sbc10b-cta] Iniciando
[sbc10a-spo] Iniciando
[sbc9a-spo] Iniciando
[sbc10b-spo] Iniciando
[sbc9b-spo] Iniciando
[sbc1a-cge] Iniciando
[sbc1b-cge] Iniciando
[sbc1b-aju] Iniciando
[sbc1a-aju] Iniciando
[sbc1a-bnu] Iniciando
[sbc1b-bnu] Iniciando
[sbc1a-cpe] Iniciando
[sbc1b-cpe] Iniciando
[sbc2a-cas] Iniciando
[sbc2b-cas] Iniciando
[sbc1a-csc] Iniciando
[sbc1b-csc] Iniciando
[sbc1a-csl] Iniciando
[sbc1b-csl] Iniciando
[sbc1a-cge] Finalizado - 83 registros
[sbc1a-foz] Iniciando
[sbc2b-cas] Finalizado - 22 registros
[sbc1b-cge] Finalizado - 83 registros
[sbc2a-cas] Finalizado - 22 registros
[sbc1b-foz] Iniciando
[sbc1a-fsa] Iniciando
[sbc1b-fsa] Iniciando
[sbc1b-cpe] Finalizado - 256 registros
[sbc1a-gvs] Iniciando
[sbc1a-aju] Finalizado - 171 registros
[sbc1b-gvs] Iniciando
[sbc1b-csl] Finalizado - 233 registros
[sbc1a-jpa] Iniciando
[sbc1a-csc] Finalizado - 302 registros
[sbc1b-jpa] Iniciando
[sbc1b-csc] Finalizado - 302 registros
[sbc1a-mco] Iniciando
[sbc9b-spo] Finalizado - 1436 registros
[sbc1b-mco] Iniciando
[sbc1b-bnu] Finalizado - 234 registros
[sbc1a-ntl] Iniciando
[sbc1a-csl] Finalizado - 233 registros
[sbc1b-ntl] Iniciando
[sbc1a-bnu] Finalizado - 234 registros
[sbc2a-pae] Iniciando
[sbc1a-cpe] Finalizado - 501 registros
[sbc2b-pae] Iniciando
[sbc10a-spo] Finalizado - 1243 registros
[sbc2a-rce] Iniciando
[sbc1a-foz] Finalizado - 150 registros
[sbc2b-rce] Iniciando
[sbc1b-gvs] Finalizado - 96 registros
[sbc3a-rjo] Iniciando
[sbc1a-jpa] Finalizado - 103 registros
[sbc3b-rjo] Iniciando
[sbc1a-gvs] Finalizado - 96 registros
[sbc3a-sdr] Iniciando
[sbc1b-aju] Finalizado - 171 registros
[sbc3b-sdr] Iniciando
[sbc2b-pae] Finalizado - 56 registros
[sbc2a-bhe] Iniciando
[sbc10b-spo] Finalizado - 1280 registros
[sbc2b-bhe] Iniciando
[sbc10a-cta] Finalizado - 1109 registros
[sbc2a-bsa] Iniciando
[sbc9a-spo] Finalizado - 1436 registros
[sbc2b-bsa] Iniciando
[sbc10b-cta] Finalizado - 1621 registros
[sbc2a-pae] Finalizado - 312 registros
[sbc1b-foz] Finalizado - 150 registros
[sbc1b-jpa] Finalizado - 103 registros
[sbc1a-mco] Finalizado - 166 registros
[sbc1b-ntl] Finalizado - 129 registros
[sbc1a-ntl] Finalizado - 129 registros
[sbc1a-fsa] Finalizado - 315 registros
[sbc1b-mco] Finalizado - 166 registros
[sbc1b-fsa] Finalizado - 315 registros
[sbc2b-bhe] Finalizado - 137 registros
[sbc2a-rce] Finalizado - 286 registros
[sbc3b-rjo] Finalizado - 563 registros
[sbc3a-rjo] Finalizado - 1075 registros
[sbc2a-bsa] Finalizado - 272 registros
[sbc2b-bsa] Finalizado - 784 registros
[sbc3a-sdr] Finalizado - 490 registros
[sbc3b-sdr] Finalizado - 746 registros
[sbc2a-bhe] Finalizado - 907 registros
[sbc2b-rce] Finalizado - 542 registros
Arquivo gerado: /home/capacidade/SBC/GENBAND/tgrpRel/rest/sbcLogs/base_20261112_234116.csv

Processamento concluído.
````

Os arquivos finais irão ficar dentro do diretório:

`/home/capacidade/SBC/GENBAND/tgrpRel/rest/sbcLogs`


## 2. Consulta Genband V1


Os SBCS Q21 Genband na V1, estão congelados. Foi gerado um relatorio com o arquivo de backup dos servidores, 
dia 28/08/2026 onde todos os SBCs que estão congelados para B2B. 
Foi gerado o arquivo .xml com o comando `cli db export` 

Exemplo arquivo: 28082026_bkp_ngn-br-sjc-rh-sbc1_2.xml

Todos os arquivos são copiados para o servidor do VTC [eng-core-voz], diretorio: 

`/home/eng-core-voz/SBC/GENBAND`

Apos isso com o programa parserGenband_db.sh, ordenamento de colunas, e remoçao de assinantes registrados: grep -v dynamic, 
o .csv final foi carregado na base igual os demais.


Arquivo `parserGenband_db.sh`: 

```bash
#!/bin/bash

OUT="resultado.csv"

# Data da coleta
DATA=$(date +%Y%m%d)

# Cabeçalho
echo '"Data","SBC","SRNO","UPORT","IPADDR","CONTACT","PAIDUSER_NAME","XCALLS","CUSTID","SRCITG","DTG","SRCETG","REALM","DTGC","NEW_TGC_INGRESS","FMM_INGRESS_PROFILE"' > "$OUT"

for ARQ in *bkp_*.xml
do
    [ -f "$ARQ" ] || continue

    SBC="${ARQ##*bkp_}"
    SBC="${SBC%.xml}"
    echo "Pasing $SBC ..."
    xmlstarlet sel -T -t \
        -m "//E" \
        -o "$DATA" -o ',' \
        -o "${SBC}" -o ',' \
        -v 'normalize-space(SRNO)' -o ',' \
        -v 'normalize-space(UPORT)' -o ',' \
        -v 'normalize-space(IPADDR)' -o ',' \
        -v 'normalize-space(CONTACT)' -o ',' \
        -v 'normalize-space(PAIDUSER_NAME)' -o ',' \
        -v 'normalize-space(XCALLS)' -o ',' \
        -v 'normalize-space(CUSTID)' -o ',' \
        -v 'normalize-space(SRCITG)' -o ',' \
        -v 'normalize-space(DTG)' -o ',' \
        -v 'normalize-space(SRCETG)' -o ',' \
        -v 'normalize-space(REALM)' -o ',' \
        -v 'normalize-space(DTGC)' -o ',' \
        -v 'normalize-space(NEW_TGC_INGRESS)' -o ',' \
        -v 'normalize-space(FMM_INGRESS_PROFILE)' \
        -n \
        "$ARQ" >> "$OUT"
    echo "Preview $SBC"
    grep $SBC $OUT | head
done
sed -i 's/" \?//g' "$OUT"  
echo "Arquivo gerado: $OUT"
```

## 3. Consulta Acme

Diretório raiz:
`/home/capacidade/SBC/ACME/tgrpRel/rest`

Preparação, deve-se primeiramente listar os SBCs, que serão parte da consulta e deixar a base de forma legivel, com valores unicos para ser usada. 

Arquivos:\
`login.sh`\
`login.json`\
`get_sbcDeviceList.sh`\
`Parsing.sh`

Arquivo resultado:
sbcDeviceList.xml
awk -F',' '!seen[$3]++' sbcDeviceList.csv > sbcDeviceList_unico.csv
sbcDeviceList_unico.csv


Arquivo `login.sh`:

```bash
curl -k -X POST "http://10.200.15.121:8080/rest/v1.3/admin/login" \
-H "Content-Type: application/json; charset=utf-8" \
-d @/home/capacidade/SBC/ACME/tgrpRel/rest/login.json | xmlstarlet sel -t -m "session" -v "sessionId" -b > /home/capacidade/SBC/ACME/tgrpRel/rest/cookie
```

Arquivo `login.json`:
```json
{
  "userName": "sbctools",
  "password": "r3st@piNNc"
}
```

Exemplo arquivo cookie: `8F62522D2D59AE65E0BAA5D716F826C6.tomcat1`

Arquivo `get_sbcDeviceList.sh`:
```bash
curl -k -X GET "http://10.200.15.121:8080/rest/v1.3/configuration/devices" \
  -H "Cookie: JSESSIONID=$(<cookie); Path=/rest; HttpOnly" | xmllint --format - > sbcDeviceList.xml

xmlstarlet sel \
  -t -m "/devices/device" \
  -v "id" -o "," \
  -v "ip" -o "," \
  -v "nfName" \
  -n sbcDeviceList.xml > sbcDeviceList.csv
```

Arquivo `sbcDeviceList.xml`:
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<devices>
  <device>
    <bootstrapState>Activated</bootstrapState>
    <connectivityStatus>false</connectivityStatus>
    <id>ID17</id>
    <ip>10.113.64.75</ip>
    <key>NGN-BR-BHE-FU-SBC1_10.113.64.75</key>
    <manageable>true</manageable>
    <name>NGN-BR-BHE-FU-SBC1</name>
    <nfId>ID26</nfId>
    <nfName>NGN-BR-BHE-FU-SBC1_NGN-BR-BHE-FU-SBC2</nfName>
    <parentGroupId>ID26</parentGroupId>
    <platformInfo>
      <platform>4500</platform>
      <serialNumber>--</serialNumber>
    </platformInfo>
    <softwareInfo>
      <configVersion>52</configVersion>
      <version>SCZ730m3p5</version>
    </softwareInfo>
    <lastOperation>SaveActivate</lastOperation>
    <loadedConfigVersion>51</loadedConfigVersion>
    <pendingChanges>1</pendingChanges>
    <status>failed</status>
    <statusChangeTime>Thu Apr 20 12:04:07 BRT 2023</statusChangeTime>
  </device>
  <device>
    <bootstrapState>Activated</bootstrapState>
    <connectivityStatus>false</connectivityStatus>
    <id>ID18</id>
    <ip>10.113.64.76</ip>
    <key>NGN-BR-BHE-FU-SBC2_10.113.64.76</key>
    <manageable>true</manageable>
    <name>NGN-BR-BHE-FU-SBC2</name>
    <nfId>ID26</nfId>
    <nfName>NGN-BR-BHE-FU-SBC1_NGN-BR-BHE-FU-SBC2</nfName>
    <parentGroupId>ID26</parentGroupId>
    <platformInfo>
      <platform>4500</platform>
      <serialNumber>--</serialNumber>
    </platformInfo>
    <softwareInfo>
      <configVersion>52</configVersion>
      <version>SCZ730m3p5</version>
    </softwareInfo>
    <lastOperation>SaveActivate</lastOperation>
    <loadedConfigVersion>51</loadedConfigVersion>
    <pendingChanges>1</pendingChanges>
    <status>failed</status>
    <statusChangeTime>Thu Apr 20 12:04:07 BRT 2023</statusChangeTime>
  </device>
  .
  .
  .

</devices>
```
Arquivo `sbcDeviceList.csv`:

```bash
> head sbcDeviceList.csv
ID19,10.241.32.68,sbc1a-soo_sbc1b-soo
ID20,10.241.32.67,sbc1a-soo_sbc1b-soo
ID21,10.47.192.58,sbc1a-pta_sbc1b-pta
ID22,10.47.192.57,sbc1a-pta_sbc1b-pta
ID23,10.214.8.96,sbc1a-bru_sbc1b-bru
ID24,10.214.8.97,sbc1a-bru_sbc1b-bru
ID27,10.214.32.98,SBC8A-SPO_SBC8B-SPO
ID28,10.214.32.99,SBC8A-SPO_SBC8B-SPO
ID29,10.209.0.65,sbc1a-grs_sbc1b-grs
ID30,10.209.0.66,sbc1a-grs_sbc1b-grs
.
.
> cat sbcDeviceList_unico.csv
ID19,10.241.32.68,sbc1a-soo_sbc1b-soo
ID21,10.47.192.58,sbc1a-pta_sbc1b-pta
ID23,10.214.8.96,sbc1a-bru_sbc1b-bru
ID27,10.214.32.98,SBC8A-SPO_SBC8B-SPO
ID29,10.209.0.65,sbc1a-grs_sbc1b-grs
ID31,10.47.204.47,sbc1a-blm_sbc1b-blm
ID33,10.185.192.2,SBC1A-CRU_SBC1B-CRU
ID35,10.141.40.184,sbc1a-lda_sbc1b-lda
ID37,10.47.216.23,sbc1a-sls_sbc1b-sls
ID39,10.161.48.15,sbc1a-cba_sbc1b-cba
ID41,10.47.228.53,sbc1a-vca_sbc1b-vca
ID45,10.209.32.66,sbc1a-sne_sbc1b-sne
ID47,10.47.236.55,sbc1a-tsa_sbc1b-tsa
ID49,10.209.160.187,sbc2a-spo_sbc2b-spo
ID51,10.221.178.21,sbc1a-vrd_sbc1b-vrd
ID53,10.185.0.154,sbc1a-fla_sbc1b-fla
ID55,10.141.32.180,sbc1b-mga_sbc1a-mga
ID59,10.141.72.189,sbc1a-fns_sbc1b-fns
ID63,10.227.0.205,sbc1a-vta_sbc1b-vta
ID67,10.164.0.98,sbc3b-bsa_sbc3a-bsa
ID71,10.161.88.179,sbc1a-gna_sbc1b-gna
ID73,10.141.88.158,sbc1a-jve_sbc1b_jve
```

Após isso deve-se rodar o arquivo `consultaSBC.sh` onde ele roda o `login.sh`, `get_sbcAgentsList.sh` para pegar os clientes e depois o `get_sbcAgentsInfo.sh` para pegar todos os parametros configurados para cada cliente. 

Arquivo `consultaSBC.sh`:
```bash
#!/bin/bash
DATE=$(date +%Y%m%d_%H%M%S)
OUTFILE="/home/capacidade/SBC/ACME/tgrpRel/rest/sbcLogs/base_${DATE}.csv"

echo "Logando..."
/home/capacidade/SBC/ACME/tgrpRel/rest/login.sh
echo "Login OK..."
echo "Gerando lista de Agentes:"
/home/capacidade/SBC/ACME/tgrpRel/rest/get_sbcAgentsList.sh
echo "Gerando Info dos Agentes."
/home/capacidade/SBC/ACME/tgrpRel/rest/get_sbcAgentsInfo.sh

cp /home/capacidade/SBC/ACME/tgrpRel/rest/sbcLogs/AgentsInfo.csv $OUTFILE
```
Arquivo `get_sbcAgentsList.sh`:
```bash
#!/bin/bash
###########################################################
# CONFIGURAÇÕES
###########################################################

BASE_URL="http://10.200.15.121:8080"

LIMITATION=10

# Quantos DEVICE_IDs simultâneos
DEVICE_PARALLEL=10

# Quantas páginas simultâneas dentro de cada DEVICE
PAGE_PARALLEL=5

COOKIE=$(cat /home/capacidade/SBC/ACME/tgrpRel/rest/cookie)

LOGDIR="/home/capacidade/SBC/ACME/tgrpRel/rest/sbcLogs"
mkdir -p "${LOGDIR}"

OUTFILE="${LOGDIR}/AgentsOnly.csv"
MASTERLOG="${LOGDIR}/AgentsOnly.log"

TMPDIR=$(mktemp -d)

export BASE_URL
export LIMITATION
export COOKIE
export TMPDIR
export LOGDIR
export PAGE_PARALLEL

###########################################################
# LIMPEZA
###########################################################

> "${OUTFILE}"
> "${MASTERLOG}"

###########################################################
# LOG
###########################################################

log() {
    local DEVICE_ID="$1"
    shift

    local MSG="$*"

    echo "$(date '+%Y-%m-%d %H:%M:%S') [${DEVICE_ID}] ${MSG}" \
        >> "${LOGDIR}/${DEVICE_ID}.log"
}

export -f log

###########################################################
# CONSULTA UMA PÁGINA
###########################################################

consulta_pagina() {

    local DEVICE_ID="$1"
    local DEVICE_NAME="$2"
    local OFFSET="$3"

    XML=$(curl -sk \
        "${BASE_URL}/rest/v1.3/configuration/deviceConfigs/${DEVICE_ID}/configElements?elementTypePath=sessionAgent&limitation=${LIMITATION}&offset=${OFFSET}" \
        -H "Cookie: JSESSIONID=${COOKIE}; Path=/rest; HttpOnly")

    xmllint --format - <<< "$XML" 2>/dev/null |
    awk '
        /<name>hostname<\/name>/ {f=1}
        f && /<value>/ {
            gsub(/.*<value>|<\/value>.*/,"")
            print
            f=0
        }
    ' |
    while read -r AGENT
    do
        [[ -z "$AGENT" ]] && continue

        echo "${DEVICE_ID},${DEVICE_NAME},${AGENT}" \
            >> "${TMPDIR}/${DEVICE_ID}.csv"

        log "${DEVICE_ID}" \
            "Agent encontrado (pagina ${OFFSET}): ${AGENT}"
    done
}

export -f consulta_pagina

###########################################################
# PROCESSA UM DEVICE
###########################################################

process_device() {

    local DEVICE_ID="$1"
    local DEVICE_IP="$2"
    local DEVICE_NAME="$3"

    log "${DEVICE_ID}" \
        "=================================================="

    log "${DEVICE_ID}" \
        "Iniciando processamento (${DEVICE_NAME})"

    #######################################################
    # LOAD CONFIG
    #######################################################

    LOAD_RESPONSE=$(curl -sk -i \
        -w "\nHTTP_CODE;%{http_code}" \
        "${BASE_URL}/rest/v1.3/configuration/devices/${DEVICE_ID}/loadConfig" \
        -H "Cookie: JSESSIONID=${COOKIE}; Path=/rest; HttpOnly")

    HTTP_CODE=$(echo "$LOAD_RESPONSE" | awk -F';' '/HTTP_CODE/ {print $2}')

    log "${DEVICE_ID}" \
        "HTTP LoadConfig: ${HTTP_CODE}"

    if [[ "$HTTP_CODE" != "200" ]]; then
        log "${DEVICE_ID}" \
            "ERRO loadConfig"

        return
    fi

    sleep 2

    #######################################################
    # DESCOBRE TOTAL
    #######################################################

    XML=$(curl -sk \
        "${BASE_URL}/rest/v1.3/configuration/deviceConfigs/${DEVICE_ID}/configElements?elementTypePath=sessionAgent&limitation=1&offset=1" \
        -H "Cookie: JSESSIONID=${COOKIE}; Path=/rest; HttpOnly")

    TOTAL=$(echo "$XML" |
        xmllint --xpath 'string(//pageInfo/numberOfElements)' - \
        2>/dev/null)

    if [[ -z "$TOTAL" ]]; then
        log "${DEVICE_ID}" \
            "ERRO obtendo numberOfElements"

        return
    fi

    if [[ "$TOTAL" -eq 0 ]]; then
        log "${DEVICE_ID}" \
            "Nenhum agent encontrado"

        return
    fi

    PAGES=$(( (TOTAL + LIMITATION - 1) / LIMITATION ))

    log "${DEVICE_ID}" "TOTAL       : ${TOTAL}"
    log "${DEVICE_ID}" "LIMITATION  : ${LIMITATION}"
    log "${DEVICE_ID}" "PAGES       : ${PAGES}"

    #######################################################
    # PÁGINAS EM PARALELO
    #######################################################

    seq 1 "${PAGES}" | \
    xargs -n1 -P "${PAGE_PARALLEL}" -I{} \
        bash -c '
            consulta_pagina "$0" "$1" "$2"
        ' \
        "${DEVICE_ID}" \
        "${DEVICE_NAME}" \
        {}

    log "${DEVICE_ID}" "Finalizado"

}

export -f process_device

###########################################################
# PROCESSA TODOS OS DISPOSITIVOS EM PARALELO
###########################################################

cat /home/capacidade/SBC/ACME/tgrpRel/rest/sbcDeviceList_unico.csv | \
xargs -d '\n' \
      -P "${DEVICE_PARALLEL}" \
      -I{} \
      bash -c '
          IFS="," read -r DEVICE_ID DEVICE_IP DEVICE_NAME <<< "$1"

          process_device \
              "$DEVICE_ID" \
              "$DEVICE_IP" \
              "$DEVICE_NAME"
      ' _ "{}"

###########################################################
# MERGE DOS RESULTADOS
###########################################################

find "${TMPDIR}" \
    -name "*.csv" \
    -type f \
    -exec cat {} \; \
    > "${OUTFILE}"

###########################################################
# MERGE DOS LOGS
###########################################################

find "${LOGDIR}" \
    -name "*.log" \
    ! -name "$(basename "${MASTERLOG}")" \
    -type f \
    -exec cat {} \; \
    | sort \
    > "${MASTERLOG}"

###########################################################
# FINALIZAÇÃO
###########################################################

echo
echo "=========================================="
echo "Processamento concluído"
echo "CSV : ${OUTFILE}"
echo "LOG : ${MASTERLOG}"
echo "TMP : ${TMPDIR}"
echo "=========================================="
```

Arquivo `Parsing.sh`:
```bash
AGENTS=$(xmllint --format - < teste_agents.xml 2>/dev/null |
awk '/<name>hostname<\/name>/{f=1}
     f && /<value>/{
         gsub(/.*<value>|<\/value>.*/, "")
         print
         f=0
     }' |
while read -r agente; do
    echo "sbcXX,IDyy,$agente"
done)

echo "$AGENTS"
```
Arquivo `get_sbcAgentsInfo.sh`:

```bash
#!/bin/bash

COOKIE=$(</home/capacidade/SBC/ACME/tgrpRel/rest/cookie)
URL='http://10.200.15.121:8080/rest/v1.3/configuration/deviceConfigs'
IN='/home/capacidade/SBC/ACME/tgrpRel/rest/sbcLogs/AgentsOnly.csv'
OUT='/home/capacidade/SBC/ACME/tgrpRel/rest/sbcLogs/AgentsInfo.csv'

process() {

    IFS=, read -r ID NAME HOST <<< "$1"

    XML=$(curl -sk -X POST \
        "$URL/$ID/configElements/retrieve" \
        -H 'Content-Type: application/xml' \
        -H "Cookie: JSESSIONID=$COOKIE; Path=/rest; HttpOnly" \
        -d "<?xml version=\"1.0\" encoding=\"UTF-8\"?>
<configElement>
<elementTypePath>sessionAgent</elementTypePath>
<attributes>
<attribute><name>hostname</name><value>$HOST</value></attribute>
</attributes>
</configElement>")

    get_attr() {
        echo "$XML" | xmllint --xpath "string(//attribute[name='$1']/value)" - 2>/dev/null
    }

    TG=$(echo "$XML" | xmllint --xpath \
        'string(//childrenElement[elementTypePath="sessionAgent/trunkGroup"]/attributes/attribute[name="name"]/value)' \
        - 2>/dev/null)

    echo "$ID,$NAME,$HOST,\
$(get_attr realmID),\
$(get_attr ipAddress),\
$(get_attr inManipulationId),\
$(get_attr description),\
$(get_attr assertedID),\
$(get_attr maxNumSessions),\
${TG%%:*},\
${TG#*:}"
}

export -f process
export COOKIE URL

cat "$IN" | xargs -P20 -d '\n' -I{} bash -c 'process "$1"' _ "{}" > "$OUT"
```

Os próximos passos são todos automáticos. 
Step 3 Execução do arquivo `consultaSBC.sh` todo dia as 23h40min:

Linha do arquivo crontab: [ adicionar com o comando `crontab -e`, comandos `'vi'` Teclas`<Win>+<Shift>+<i>` cola a linha aperta `<Esc>` escreve e sai com `:wq!` ]

`40 23 * * * /home/capacidade/SBC/ACME/tgrpRel/rest/consultaSBC.sh >> /home/capacidade/SBC/ACME/tgrpRel/rest/logDiario.log 2>&1`

Resultados:
```bash
> head AgentsOnly.csv
ID33,SBC1A-CRU_SBC1B-CRU,10.8.189.134
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.130
ID33,SBC1A-CRU_SBC1B-CRU,172.16.2.2
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.26
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.194
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.34
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.106
ID33,SBC1A-CRU_SBC1B-CRU,10.77.17.176
ID33,SBC1A-CRU_SBC1B-CRU,10.77.17.80
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.58
.
.
.
> head AgentsInfo.csv
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.26,privaccess-voxip,172.16.0.26,,1-A0VJ3AH0VC@INSTITUTO DO CANCER INFANTIL DO AGRESTE ICIA,<sip:+558131399980@ims3.gvt.net.br>,10,rce3voxip3730,ims3.gvt.net.br
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.130,privaccess-voxip,172.16.0.130,,CRU301MVVV82@CARTAO DEMAIS DESCONTOS,<sip:+558131399898@ims3.gvt.net.br>,30,rce3voxip3030,ims3.gvt.net.br
ID33,SBC1A-CRU_SBC1B-CRU,172.16.2.2,privaccess-voxip,172.16.2.2,,CRU301MX7XPW@CLUBE DE BENEFICIOS,<sip:+558121614667@ims3.gvt.net.br>,10,rce3voxip3153,ims3.gvt.net.br
ID33,SBC1A-CRU_SBC1B-CRU,10.8.189.134,core-metaswitch-reg,10.8.189.134,,SBC2 METASWITCH,,0,,
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.194,privaccess-voxip,172.16.0.194,,CRU-301N0AVKJ@VAREJAO LEAO DOURADO LTDA,<sip:+558131399090@ims3.gvt.net.br>,10,rce3voxip3767,ims3.gvt.net.br
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.106,privaccess-voxip,172.16.0.106,,1-A7BIUAT8VC@CT_CONSTRUCOES_LTDA,<sip:+558137247707@ims3.gvt.net.br>,10,rce3voxip1478,ims3.gvt.net.br
ID33,SBC1A-CRU_SBC1B-CRU,172.16.0.34,privaccess-voxip,172.16.0.34,,1-A1UZ9JRTVC@FARMACIA_AGRESTINA,<sip:+558131381010@ims3.gvt.net.br>,6,rce3voxip3854,ims3.gvt.net.br
ID33,SBC1A-CRU_SBC1B-CRU,10.77.17.176,one-core,10.77.17.176,,P-CSCF IMS ONE CORE - SDR - SBC1,,0,,
ID33,SBC1A-CRU_SBC1B-CRU,10.77.17.80,one-core,10.77.17.80,,P-CSCF IMS ONE CORE - SDR - SBC2,,0,,
ID33,SBC1A-CRU_SBC1B-CRU,BCF-AMAFP,centralizado_core_v1,10.62.121.141,chg_trunkcontext,.BCF1-IMS-AMAFP.,,0,,
.
.
.
> 
```

```bash
> cat base_20261001_234001.csv | awk -F',' '!visto[$1]++' 
ID33,SBC1A-CRU_SBC1B-CRU,172.16.4.138,privaccess-voxip,172.16.4.138,,1_AZT8IPGHVC@INDUSTRIA DE BEBIDAS GARANHUNS LTDA,<sip:+558737624455@ims3.gvt.net.br>,10,rce3voxip12217,ims3.gvt.net.br
ID63,sbc1a-vta_sbc1b-vta,10.130.72.144,core,10.130.72.144,,P-CSCF IMS BHE,,0,,
ID35,sbc1a-lda_sbc1b-lda,10.212.64.202,core,10.212.64.202,,AS Broadsoft via SBC Sinergia,,0,,
ID53,sbc1a-fla_sbc1b-fla,172.16.1.26,privaccess-voxip,172.16.1.26,,1-8W4A43NOVC@INDUSTRIA METALURGICA DE CARLITO PAMPLONA LTDA,<sip:+558532283456@ims3.gvt.net.br>,15,rce3voxip1916,ims3.gvt.net.br
ID37,sbc1a-sls_sbc1b-sls,172.16.0.242,privaccess-voxip,172.16.0.242,,1-A4IWSJ2IVC@TEXAS COMERCIO DE AUTOMOVEIS LTDA,<sip:+559830154343@ims3.gvt.net.br>,15,rce3voxip6076,ims3.gvt.net.br
ID23,sbc1a-bru_sbc1b-bru,10.8.143.5,one-core,10.8.143.5,,P-CSCF IMS ONE CORE - MB,,0,,
ID55,sbc1b-mga_sbc1a-mga,10.140.80.144,core,10.140.80.144,,P-CSCF IMS CTA,,0,,
ID21,sbc1a-pta_sbc1b-pta,10.69.225.47,one-core-voxip,10.69.225.47,,BCF1-IMS-ONE CORE - FLA,,0,,
ID59,sbc1a-fns_sbc1b-fns,10.141.193.14,one-core-voxip,10.141.193.14,,BCF1-IMS-ONE CORE - CTA,,0,,
ID19,sbc1a-soo_sbc1b-soo,10.140.80.144,core,10.140.80.144,,P-CSCF IMS CTA,,0,,
ID67,sbc3b-bsa_sbc3a-bsa,10.58.197.176,one-core,10.58.197.176,,P-CSCF IMS ONE CORE - BSA - SBC2,,0,,
ID73,sbc1a-jve_sbc1b_jve,172.16.1.66,privaccess-voxip,172.16.1.66,,1-9S4I8Q6SVC@EFICACIA FARMACIA DE MANIPULACAO LTDA,<sip:+554732410100@ims2.gvt.net.br>,30,cta7voxip2433,ims2.gvt.net.br
ID45,sbc1a-sne_sbc1b-sne,10.220.72.144,core,10.220.72.144,,P-CSCF IMS RJO,,0,,
ID29,sbc1a-grs_sbc1b-grs,172.16.0.42,privaccess-voxip,172.16.0.42,,SPB-301MJP460@ERICSSON,<sip:+551122242000@ims1.gvt.net.br>,60,spo7voxip1850,ims1.gvt.net.br
ID49,sbc2a-spo_sbc2b-spo,sa.cs2k-sne,*,10.210.73.248,,,,0,,
ID47,sbc1a-tsa_sbc1b-tsa,10.180.72.144,core,10.180.72.144,,P-CSCF IMS RCE,,0,,
ID27,SBC8A-SPO_SBC8B-SPO,10.8.143.37,one-core,10.8.143.37,,P-CSCF IMS ONE CORE - MB,,0,,
ID39,sbc1a-cba_sbc1b-cba,10.140.76.144,core,10.140.76.144,,P-CSCF IMS MGA,,0,,
ID51,sbc1a-vrd_sbc1b-vrd,BCF-SPOMB,centralizado_core_v1,10.8.136.67,chg_trunkcontext,.BCF1-IMS-SPOMB.,,0,,
ID31,sbc1a-blm_sbc1b-blm,172.16.0.234,privaccess-voxip,172.16.0.234,,BLM-301MNDRJ5@FALCAO MONTEIRO E SALAME SERVICOS,<sip:+559132994450@ims3.gvt.net.br>,15,rce3voxip2638,ims3.gvt.net.br
ID41,sbc1a-vca_sbc1b-vca,172.16.2.138,privaccess-voxip,172.16.2.138,,1-9NCW8BL6VC@INOVAR TINTAS INDUSTRIA QUIMICA LTDA,<sip:+557734229600@ims3.gvt.net.br>,10,rce3voxip2699,ims3.gvt.net.br
```

## 4. Consulta Sonus


Inicialmente gerar a lista de SBCs

```bash
curl -k -X POST "https://10.47.9.34/sbcapi/SBC_LIST_APIService" \
-H "Content-Type: application/xml; charset=utf-8" \
-H "password: Soap@atr2019" \
-H "username: soapatr" \
-d @getSBCList.xml > sbcDeviceList.xml
```

Arquivo `getSBCList.xml`:

```xml
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
        xmlns:sbc="http://www.sonus.com/ems/api/sbc/sbcList">
  <soapenv:Header/>
    <soapenv:Body>
      <sbc:getSbcList>
        <sbcName></sbcName>
      </sbc:getSbcList>
    </soapenv:Body>
```
```xml
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/">
    <SOAP-ENV:Header xmlns:SOAP-ENV="http://schemas.xmlsoap.org/soap/envelope/"/>
    <soap:Body>
        <ns1:getSbcListResponse xmlns:ns1="http://www.sonus.com/ems/api/sbc/sbcList">
            <SbxInfo>
                <item>
                    <ip>10.230.48.5</ip>
                    <lastChangeTime>1782226401808</lastChangeTime>
                    <lastDiscoveryStatus>0</lastDiscoveryStatus>
                    <name>bavla-ngnvmi-sbc01-02</name>
                    <nodeId>10409</nodeId>
                    <snmpVersion>1</snmpVersion>
                </item>
                <item>
                    <ip>10.230.40.165</ip>
                    <lastChangeTime>1781990262769</lastChangeTime>
                    <lastDiscoveryStatus>0</lastDiscoveryStatus>
                    <name>spsef-sbcumr-sra03-04</name>
                    <nodeId>10439</nodeId>
                    <snmpVersion>1</snmpVersion>
                </item>
               .
               .
               .

                <item>
                    <ip>10.230.36.4</ip>
                    <lastChangeTime>1781217803279</lastChangeTime>
                    <lastDiscoveryStatus>0</lastDiscoveryStatus>
                    <name>rsind-ngnvmi-sbc03-04</name>
                    <nodeId>10426</nodeId>
                    <snmpVersion>1</snmpVersion>
                </item>
                <item>
                    <ip>10.230.36.20</ip>
                    <lastChangeTime>1781217711686</lastChangeTime>
                    <lastDiscoveryStatus>0</lastDiscoveryStatus>
                    <name>rsind-ngnvmi-sbc01-02</name>
                    <nodeId>10425</nodeId>
                    <snmpVersion>1</snmpVersion>
                </item>
            </SbxInfo>
        </ns1:getSbcListResponse>
    </soap:Body>
</soap:Envelope>
```

Fazer o parsing para usar a lista de SBCs gerados:

```bash
xmlstarlet sel -N soap="http://schemas.xmlsoap.org/soap/envelope/" \
-N ns1="http://www.sonus.com/ems/api/sbc/sbcList" \
-t -m "//soap:Body/ns1:getSbcListResponse/SbxInfo/item" \
-v "name" -o "," \
-v "ip" -o "," \
-v "nodeId" -o "," \
-v "lastChangeTime" -o "," \
-v "lastDiscoveryStatus" -o "," \
-v "snmpVersion" \
-n sbcDeviceList.xml > df_arquivo.log
```
```bash
>head df_arquivo.log
bavla-ngnvmi-sbc01-02,10.230.48.5,10409,1782226401808,0,1
spsef-sbcumr-sra03-04,10.230.40.165,10439,1781990262769,0,1
ngn-br-spo-mb-sbc07,10.230.216.98,10466,1782480925782,0,1
spvma-ngnvmi-sbc03-04,10.230.20.21,10432,1781217749856,0,1
ngn-br-spo-si-sbc03,10.113.124.114,10144,1781735145604,0,1
perce-ngnvmi-sbc01-02,10.230.48.20,10419,1782214589871,0,1
ngn-br-spo-pa-sbc,10.113.124.130,10467,1780891034976,0,1
ngn-br-bhe-fu-sbc5,10.229.250.244,10442,1781217740955,0,1
SBC1-ULA,10.47.128.21,10480,1781067375126,0,1
cefla-ngnvmi-sbc03-04,10.230.48.53,10405,1780146499133,0,1
.
.
.
```


Rodar o arquivo `consultaSBC.sh`

Ele irá rodar a consulta em todos os SBCs do arquivo de DeviceList e colocar no diretorio abaixo:\
`/home/capacidade/SBC/SONUS/tgrpRel/rest/sbcLogs/clientes_${SBC}.csv`

Dentro desse diretorio existe o programa `/home/capacidade/SBC/SONUS/tgrpRel/rest/sbcLogs/gerarBase.sh`

O programa consultaSBC.sh roda ele no final para gerar o formado compilado `base_${DATE}.csv`

Arquivo `consultaSBC.sh`:

```bash
#!/bin/bash

set -euo pipefail

ARQUIVO="/home/capacidade/SBC/SONUS/tgrpRel/rest/df_arquivo.log"
URL="https://10.47.9.34/sbcapi/v07_02_00/SBCService"
OUTDIR="/home/capacidade/SBC/SONUS/tgrpRel/rest/sbcLogs"

# Quantidade de consultas simultâneas
PARALLEL=20

mkdir -p "$OUTDIR"

processar_sbc() {

    local SBC="$1"

    echo "[$(date +%H:%M:%S)] Consultando ${SBC}"

    curl -k -sS \
        -X POST \
        -H "Content-Type: application/xml; charset=utf-8" \
        -H "password: Soap@atr2019" \
        -H "username: soapatr" \
        --data-binary @- \
        "$URL" <<EOF |
<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope
    xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
    xmlns:v07="http://www.sonus.com/ems/api/sbc/v07_02_00">
    <soapenv:Header/>
    <soapenv:Body>
        <v07:retrieve>
            <sbcName>${SBC}</sbcName>
            <target
                xmlns:ns5="addresscontext.v07_02_00.sbc.api.ems.sonus.com"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                xsi:type="ns5:zoneKey">
                <name></name>
                <parentKey xsi:type="xsd:string">
                    <name>default</name>
                </parentKey>
            </target>
        </v07:retrieve>
    </soapenv:Body>
</soapenv:Envelope>
EOF

    xmlstarlet sel \
        -N soap="http://schemas.xmlsoap.org/soap/envelope/" \
        -N ns1="http://www.sonus.com/ems/api/sbc/v07_02_00" \
        -t \
        -m "//soap:Body/ns1:retrieveResponse/SbcEntity/zone/sipTrunkGroup" \
        -o "_" \
        -v "../name" -o "," \
        -v "name" -o "," \
        -v "state" -o "," \
        -v "cac/callLimit" -o "," \
        -v "policy/carrier" -o "," \
        -v "policy/sipDomain" -o "," \
        -v "policy/ipSignalingPeerGroup" -o "," \
        -v "policy/digitParameterHandling/ingressDmPmRule" -o "," \
        -v "ingressIpPrefix/ipAddress" \
        -n |
    awk '
    /^_/ {
        if (NR > 1) print out
        out = substr($0,2)
        next
    }
    {
        out = out ";" $0
    }
    END {
        if (out != "") print out
    }' > "${OUTDIR}/clientes_${SBC}.csv"

    echo "[$(date +%H:%M:%S)] Finalizado ${SBC}"
}

export -f processar_sbc
export URL OUTDIR

cut -d',' -f1 "$ARQUIVO" |
grep -v '^$' |
sort -u |
xargs -I{} -P "${PARALLEL}" bash -c 'processar_sbc "$@"' _ {}


/home/capacidade/SBC/SONUS/tgrpRel/rest/sbcLogs/gerarBase.sh
```

```bash
>cat base_20261009_235303.csv | awk -F',' '!visto[$1]++' | grep voxip | head
ngn-br-arq-ad-sbc01,ZN_CORE_VOXIP,CORE_IMS_BCF,enabled,,voxip,IMS4.VIVO.NET.BR,,,10.8.136.67;10.8.140.67;10.8.154.26;10.8.188.122;10.105.51.97;10.105.55.99;10.229.101.44;10.229.111.44
ngn-br-bru-ac-sbc,core-voxip,CORE_IMS_CORPORATE_BCF,enabled,,0000,IMS4.VIVO.NET.BR,,,10.8.136.67;10.8.140.67;10.8.154.26;10.8.188.122;10.11.229.130;10.105.51.97;10.105.55.99;10.229.101.44;10.229.111.44
ngn-br-cas-bon-sbc,core-voxip,CORE_IMS_BCF,enabled,,voxip,IMS4.VIVO.NET.BR,,,10.8.136.67;10.8.140.67;10.8.154.26;10.8.188.122;10.11.229.130;10.105.51.97;10.105.55.99;10.229.101.44;10.229.111.44
ngn-br-cas-ct-sbc,core-voxip,CORE_IMS_BCF,enabled,,voxip,IMS4.VIVO.NET.BR,,,10.8.136.67;10.8.140.67;10.8.154.26;10.8.188.122;10.11.229.130;10.105.51.97;10.105.55.99;10.229.101.44;10.229.111.44
ngn-br-cta-vm-sbc3_4,ZN_CORE_VOXIP,CORE_IMS_BCF,enabled,,voxip,IMS2.GVT.NET.BR,,,10.140.76.163;10.140.80.163
ngn-br-mia-ja-sbc,ZN_CORE_VOXIP,CORE_IMS_BCF,enabled,,voxip,IMS4.VIVO.NET.BR,,,10.8.136.67;10.8.140.67;10.8.154.26;10.8.188.122;10.105.51.97;10.105.55.99;10.229.101.44;10.229.111.44
ngn-br-pge-vc-sbc,ZN_CORE_VOXIP,CORE_IMS_BCF,enabled,,voxip,IMS4.VIVO.NET.BR,,,10.8.136.67;10.8.140.67;10.8.154.26;10.8.188.122;10.105.51.97;10.105.55.99;10.229.101.44;10.229.111.44
ngn-br-poa-ind-sbc1,ZN_CORE_VOXIP,CORE_IMS_BCF,enabled,,voxip,IMS2.GVT.NET.BR,,,10.140.76.163;10.140.80.163;10.229.101.44;10.229.111.44
ngn-br-sbo-pl-sbc,zn_yellum,spobevoxip26595,enabled,120,voxip2,IMS4.VIVO.NET.BR,spobevoxip26595_IPSIGP,voxip26595_1130197000,10.7.111.66
ngn-br-spo-be-sbc3_4,ZN_CORE_VOXIP,CORE_IMS_BCF,enabled,,voxip,IMS4.VIVO.NET.BR,,,10.8.136.67;10.8.140.67;10.8.154.26;10.8.188.122;10.105.51.97;10.105.55.99;10.229.101.44;10.229.111.44
.
.
.
```

Linha do arquivo crontab: [ adicionar com o comando `crontab -e`, comandos `'vi'` Teclas `<Win>+<Shift>+<i>` cola a linha aperta `<Esc>` escreve e sai com `:wq!` ]\
`40 23 * * * /home/capacidade/SBC/SONUS/tgrpRel/rest/consultaSBC.sh >> /home/capacidade/SBC/SONUS/tgrpRel/rest/logDiario.log 2>&1`


## 5. Consulta Huawei SRRs

Arquivo `getISIPTG_SRR.bat`

```bat
:: filepath: C:\Users\G0052677\Documents\Code\SRR\getISIPTG_SRR.py
@echo off
start "Monitor Arquivos SBC" plink.exe -ssh capacidade@10.200.35.226 -pw capacidade -t -no-antispoof "stty cols 140 rows 40 && cd /home/capacidade/SBC/HUAWEI/SRR && watch -n 0 ls -l"
cd /d "C:\Users\G0052677\Documents\Code\SRR"
start /b & "C:\Program Files\Python312\python.exe" c:/Users/G0052677/Documents/Code/SRR/getISIPTG_SRR.py
pause
```

Arquivo `getISIPTG_SRR.py`:
```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
import socket
import time
import logging
from pathlib import Path
from datetime import datetime
import paramiko
# ==========================================================
# CONFIG
# ==========================================================
HOST = "201.69.136.99"
PORT = 31114
USER = "G0052677"
PASSWORD = "[Gvt]1404"
NES = [
    "NGN-BR-SPO-CO-SRR1",
    "NGN-BR-SPO-IB-SRR1",
]
SFTP_HOST = "10.200.35.226"
SFTP_PORT = 22
SFTP_USER = "capacidade"
SFTP_PASSWORD = "capacidade"
DESTINO_SFTP = "/home/capacidade/SBC/HUAWEI/SRR"
BASE_DIR = Path(__file__).resolve().parent
# ==========================================================
# LOG
# ==========================================================
logging.basicConfig(
    filename=BASE_DIR / "coleta_huawei.log",
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(message)s"
)
# ==========================================================
# SOCKET
# ==========================================================
def conectar():
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.settimeout(10)
    logging.info(
        f"Conectando {HOST}:{PORT}"
    )
    sock.connect((HOST, PORT))
    logging.info("Conectado")
    return sock

def receber_resposta(sock, espera_final=3):
    """
    Lê tudo até ficar alguns segundos sem receber dados.
    """
    resposta = ""
    while True:
        try:
            dados = sock.recv(8192)
            if not dados:
                break
            texto = dados.decode(
                "utf-8",
                errors="ignore"
            )
            print(texto, end="", flush=True)
            resposta += texto
        except socket.timeout:
            break
    return resposta

def executar(sock, comando, timeout=5):
    print("\n" + "=" * 80)
    print(f"COMANDO: {comando}")
    print("=" * 80)
    logging.info(comando)
    sock.sendall((comando + "\r\n").encode())
    fim = time.time() + timeout
    resposta = ""
    while time.time() < fim:
        try:
            dados = sock.recv(8192)
            if dados:
                texto = dados.decode(
                    "utf-8",
                    errors="ignore"
                )
                print(texto, end="", flush=True)
                resposta += texto
                fim = time.time() + timeout
        except socket.timeout:
            pass
    return resposta
# ==========================================================
# ARQUIVOS
# ==========================================================
def salvar_log(ne, conteudo):
    ts = datetime.now().strftime(
        "%Y-%m-%d_%H%M%S"
    )
    arquivo = BASE_DIR / f"{ts}_{ne}.log"
    with open(
        arquivo,
        "w",
        encoding="utf-8"
    ) as f:
        f.write(conteudo)
    logging.info(
        f"Arquivo gerado {arquivo}"
    )
    return arquivo
# ==========================================================
# SFTP
# ==========================================================
def upload_sftp(arquivo):
    logging.info(
        f"Upload {arquivo.name}"
    )
    transport = paramiko.Transport(
        (SFTP_HOST, SFTP_PORT)
    )
    transport.connect(
        username=SFTP_USER,
        password=SFTP_PASSWORD
    )
    sftp = paramiko.SFTPClient.from_transport(
        transport
    )
    remoto = f"{DESTINO_SFTP}/{arquivo.name}"
    sftp.put(
        str(arquivo),
        remoto
    )
    sftp.close()
    transport.close()

def executar_du():
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(
        paramiko.AutoAddPolicy()
    )
    ssh.connect(
        hostname=SFTP_HOST,
        username=SFTP_USER,
        password=SFTP_PASSWORD
    )
    stdin, stdout, stderr = ssh.exec_command(
        f"du -ha {DESTINO_SFTP}"
    )
    resultado = stdout.read().decode()
    print("\n===== DU -HA =====")
    print(resultado)
    logging.info(resultado)
    ssh.close()
# ==========================================================
# MAIN
# ==========================================================
def main():
    arquivos = []
    sock = conectar()
    executar(
        sock,
        f'LGI:OP="{USER}",PWD="{PASSWORD}";'
    )
    for ne in NES:
        executar(
            sock,
            f'REG NE:NAME="{ne}";'
        )
        resposta = executar(
            sock,
            "LST ISIPTG:;",
            timeout=20
        )
        arq = salvar_log(
            ne,
            resposta
        )
        arquivos.append(arq)
        executar(
            sock,
            f'UNREG NE:NAME="{ne}";'
        )
    sock.close()
    for arq in arquivos:
        upload_sftp(arq)
    executar_du()
    logging.info(
        "PROCESSO FINALIZADO"
    )

if __name__ == "__main__":
    main()
```


O programa roda na maquina da pessoa devido a conectividade com taishan. `getISIPTG_SRR.py`
Ele consulta os elementos, salva o .log e envia para o servidor Server-Eng_CX  [ svuxpexcap01 ]   IP: 10.200.35.226

Dentro do servidor tem 3 programas:

Um programa chamado `watch_srr.sh` que fica de olho nos arquivos novos que entram no diretorio:\
`/home/capacidade/SBC/HUAWEI/SRR`
Se tiver arquivos novos ele mais tres programas um para fazer parser, outro para fazer um filtro e outro para carregar o resultado na base. 

`parser_isiptg.sh`
`filtra_csv.sh`
`loadBase.sh`

Arquivo `watch_srr.sh`:

```bash
#!/bin/bash
DIR="/home/capacidade/SBC/HUAWEI/SRR"
INTERVAL=30
cd "$DIR" || exit 1
echo "$(date '+%F %T') - Monitor iniciado"
while true
do
    FILES=(*SRR*.log)
    if [ ! -e "${FILES[0]}" ]; then
        sleep "$INTERVAL"
        continue
    fi
    QTD=$(ls -1 *SRR*.log 2>/dev/null | wc -l)
    if [ "$QTD" -ge 2 ]; then
        F1=$(ls -1 *SRR*.log | head -1)
        F2=$(ls -1 *SRR*.log | head -2 | tail -1)
        CHAVE="$F1|$F2"
        if [ "$CHAVE" != "$ULTIMO_PROCESSAMENTO" ]; then
            echo
            echo "$(date '+%F %T') - Encontrados:"
            echo "  $F1"
            echo "  $F2"
            echo "$(date '+%F %T') - Executando parser_isiptg.sh"
            ./parser_isiptg.sh
            CSV=$(ls -1t base_*.csv 2>/dev/null | head -1)
            if [ -n "$CSV" ]; then
                echo "$(date '+%F %T') - Executando filtra_csv.sh $CSV"
                ./filtra_csv.sh "$CSV"
                CSV_LIGHT="${CSV%.csv}_light.csv"
                if [ -f "$CSV_LIGHT" ]; then
                    echo "$(date '+%F %T') - Executando loadBase.sh $CSV_LIGHT"
                    ./loadBase.sh "$CSV_LIGHT"
                fi
            fi
            ULTIMO_PROCESSAMENTO="$CHAVE"
            echo "$(date '+%F %T') - Processamento concluído"
        fi
    fi
    sleep "$INTERVAL"
done
```

Arquivo `parser_isiptg.sh`:

```bash
#!/bin/bash
OUTFILE="base_$(date +%F_%H%M%S).csv"
gawk '
###############################################################################
# CSV escape
###############################################################################
function csv(x) {
    gsub(/"/,"\"\"",x)
    return "\"" x "\""
}
###############################################################################
# Trim
###############################################################################
function trim(x) {
    sub(/^[ \t\r\n]+/, "", x)
    sub(/[ \t\r\n]+$/, "", x)
    return x
}
###############################################################################
# Descobre posições das colunas a partir do cabeçalho
###############################################################################
function build_columns(line,   i,c,start,len,name,colcount) {
    delete COL_START
    delete COL_END
    delete COL_NAME
    colcount = 0
    start = 0
    for (i=1; i<=length(line); i++) {
        c = substr(line,i,1)
        if (c != " " && (i==1 || substr(line,i-1,1)==" ")) {
            start=i
            while (i <= length(line) &&
                   !(substr(line,i,1)==" " &&
                     substr(line,i+1,1)==" ")) {
                i++
            }
            len=i-start
            name=trim(substr(line,start,len))
            if (length(name)>0) {
                colcount++
                COL_NAME[colcount]=name
                COL_START[colcount]=start
            }
        }
    }
    for(i=1;i<colcount;i++)
        COL_END[i]=COL_START[i+1]-1
    COL_END[colcount]=99999
    return colcount
}
###############################################################################
# Emite CSV
###############################################################################
function emit_record(line,   i,val) {
    if (trim(line)=="")
        return
    printf "%s,%s",
           csv(FILE_DATE),
           csv(FILE_SRR)
    for(i=1;i<=NCOLS;i++) {
        val=substr(line,
                   COL_START[i],
                   COL_END[i]-COL_START[i]+1)
        val=trim(val)
        printf ",%s",csv(val)
    }
    printf "\n"
}
BEGIN{
    header_written=0
    in_table=0
}
###############################################################################
# Captura SRR e Data
###############################################################################
/^\+\+\+/ {
    if (match($0,/MENAME:([^*]+)/))
        FILE_SRR=substr($0,RSTART+7,RLENGTH-7)
    if (match($0,/[0-9]{4}-[0-9]{2}-[0-9]{2}/))
        FILE_DATE=substr($0,RSTART,10)
    next
}
###############################################################################
# Detecta cabeçalho principal
###############################################################################
/Trunk group name/ && /TG ID/ {
    NCOLS=build_columns($0)
    if (!header_written) {
        printf "\"Data\",\"SRR\""
        for(i=1;i<=NCOLS;i++)
            printf ",%s",csv(COL_NAME[i])
        printf "\n"
        header_written=1
    }
    in_table=1
    next
}

###############################################################################
# Fim de página Huawei
###############################################################################
/^To be continued/ { next }
/^---[ ]*END/      { next }

###############################################################################
# Ignorar controle
###############################################################################
/^RETCODE/ { next }
/^The result is as follows/ { next }
/^------------------------/ { next }
/^%%/ { next }
/^O&M/ { next }
/^$/ { next }

###############################################################################
# Dados
###############################################################################
in_table {
    line=$0
    if (line ~ /^\+\+\+/)
        next
    if (line ~ /^%%/)
        next
    if (line ~ /^RETCODE/)
        next
    if (line ~ /^The result is as follows/)
        next
    if (line ~ /^------------------------/)
        next
    if (trim(line)=="")
        next
    emit_record(line)
}
' *.log > "$OUTFILE"
echo
echo "CSV gerado:"
echo "  $OUTFILE"
echo
echo "Total de linhas:"
wc -l "$OUTFILE"
rm -f *SRR*.log

```

Arquivo `filtra_csv.sh`:


```bash
#!/bin/bash
mv /home/capacidade/SBC/HUAWEI/SRR/*_light.csv /home/capacidade/SBC/HUAWEI/SRR/bkup
ARQ="$1"
if [ -z "$ARQ" ]; then
    echo "Uso: $0 arquivo.csv"
    exit 1
fi
SAIDA="/home/capacidade/SBC/HUAWEI/SRR/${ARQ%.csv}_light.csv"
gawk -F',' '
BEGIN {
    OFS=","
    remove["Link information"]
    remove["Enable TCP fallback"]
    remove["Local port number"]
    remove["Peer IP address type"]
    remove["Peer IPv6 address"]
    remove["Peer port number"]
    remove["Priority of media IP address type"]
    remove["Message absence triggered detection"]
    remove["Trigger period"]
    remove["Delete CIC"]
    remove["CAC policy set name 3"]
    remove["SIP policy name"]
    remove["Bearer control policy name"]
    remove["Transcode capability name"]
    remove["Automatic response to OPTIONS"]
    remove["Country code"]
    remove["Send COLP request"]
    remove["Calling number address attribute"]
    remove["Called number address attribute"]
    remove["Encryption policy set name"]
    remove["Fallback MTU for TCP messages"]
    remove["List ignored ports for inbound trunk"]
    remove["Outgoing trunk route selection policy"]
    remove["Enable incoming trunk number change"]
    remove["Incoming trunk number change policy"]
    remove["Inbound trunk number change group name"]
    remove["Enable outgoing trunk number change"]
    remove["Outgoing trunk number change policy"]
    remove["Outbound trunk number change group name"]
    remove["Certificate subject domain"]
    remove["Set QoS threshold"]
    remove["Critical alarm threshold for packet loss rate(%)"]
    remove["Major alarm threshold for packet loss rate(%)"]
    remove["Critical alarm threshold for RTD(ms)"]
    remove["Major alarm threshold for RTD(ms)"]
    remove["Critical alarm threshold for jitter(ms)"]
    remove["Major alarm threshold for jitter(ms)"]
    remove["Critical alarm threshold for MOS"]
    remove["Major alarm threshold for MOS"]
    remove["Routing percentage(%)"]
    remove["Clearance time for QoS critical alarm threshold(s)"]
    remove["Hide incoming trunk group name"]
    remove["Timeout time(s)"]
    remove["Fault detection counts"]
    remove["Fault recovery duration(s)"]
    remove["Number policy type"]
    remove["Calling number policy"]
    remove["Calling number policy enabled"]
    remove["Incoming calling blacklist policy"]
    remove["Calling number group name"]
    remove["Calling number deny response code"]
    remove["Incoming calling whitelist usergroup name"]
    remove["Incoming calling blacklist usergroup name"]
    remove["Called number policy"]
    remove["Called number policy enabled"]
    remove["Called number group name"]
    remove["Called number deny response code"]
    remove["Incoming called whitelist usergroup name"]
    remove["Incoming called blacklist usergroup name"]
    remove["Header containing calling number"]
    remove["Managed object group"]
    remove["Transport value"]
    remove["Match peer port"]
    remove["Fallback MTU for static TCP messages"]
    remove["Clearmode ptime"]
    remove["Reserved1"]
    remove["Reserved2"]
    remove["Reserved3"]
    remove["Reserved4"]
    remove["Customize OPTIONS detection"]
    remove["Confirm check interval(s)"]
    remove["Fault check interval(s)"]
    remove["Check times from confirm to fault"]
    remove["Check times from fault to normal"]
    remove["Enable flexible routing"]
    remove["Flexible routing policy"]
}
NR==1 {
    for(i=1;i<=NF;i++) {
        h=$i
        gsub(/^"/,"",h)
        gsub(/"$/,"",h)
        if(!(h in remove)) {
            keep[i]=1
            out[++nout]=i
        }
        if(h=="TG ID")
            tgcol=i
    }
    for(j=1;j<=nout;j++) {
        if(j>1) printf OFS
        printf "%s", $(out[j])
    }
    printf "\n"
    next
}
{
    tg=$tgcol
    gsub(/^"/,"",tg)
    gsub(/"$/,"",tg)
    if(tg=="")
        next
    for(j=1;j<=nout;j++) {
        if(j>1) printf OFS
        printf "%s", $(out[j])
    }
    printf "\n"
}
' "$ARQ" > "$SAIDA"
echo
echo "Arquivo original : $ARQ"
echo "Arquivo reduzido : $SAIDA"
echo
wc -l "$SAIDA"
rm -f /home/capacidade/SBC/HUAWEI/SRR/$ARQ
```

Arquivo `loadBase.sh`:

```bash
#!/bin/bash

ARQ=$(ls -1t /home/capacidade/SBC/HUAWEI/SRR/base*_light.csv | head -1)

printf "Uploading to [ tgrpSrr database ]...\n"
mysql -uroot -pEngCX666 --local_infile=1 dbengcx \
-e "LOAD DATA LOCAL INFILE '$ARQ' INTO TABLE tgrpSrr FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '\"' IGNORE 1 LINES";
mysql -uroot -pEngCX666 --local_infile=1 dbengcx -e "DELETE FROM tgrpSrr WHERE data IS NULL";
printf "Finalizado OK."
```

exemplo do arquivo final.
`base_2026-11-13_074501_light.csv`



```bash
>cat base_2026-11-13_074501_light.csv | grep PBX_ | head
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_11764","PBX trunk group","access_pbx","10.12.211.34","Access_Media","Continuous heartbeat","pbx","5000","6CAPS","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","spopavoxip11764","asvox1.vivo.net.br","172"
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_11777","PBX trunk group","access_pbx","10.10.10.20","Access_Media","Disabling heartbeat","pbx","200","NULL","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","pabxid","asvox1.vivo.net.br","323"
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_1410","PBX trunk group","access_pbx","10.4.133.58","Access_Media","Continuous heartbeat","pbx","1000","146CAPS","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","brealvoxip1410","asvox1.vivo.net.br","57"
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_2000_1","PBX trunk group","access_pbx","10.30.255.242","Access_Media","Disabling heartbeat","pbx","300","102CAPS","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","bsavoxip2000","asvox1.vivo.net.br","92"
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_2000_2","PBX trunk group","access_pbx","10.30.255.243","Access_Media","Disabling heartbeat","pbx","300","102CAPS","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","bsavoxip2000","asvox1.vivo.net.br","507"
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_2000_3","PBX trunk group","access_pbx","10.30.255.244","Access_Media","Disabling heartbeat","pbx","300","102CAPS","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","bsavoxip2000","asvox1.vivo.net.br","508"
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_2000_4","PBX trunk group","access_pbx","10.30.255.245","Access_Media","Disabling heartbeat","pbx","300","102CAPS","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","bsavoxip2000","asvox1.vivo.net.br","509"
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_2001","PBX trunk group","access_pbx","10.28.255.226","Access_Media","Disabling heartbeat","pbx","180","180CAPS","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","cslvoxip2001","asvox1.vivo.net.br","96"
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_2002","PBX trunk group","access_pbx","10.24.255.210","Access_Media","Disabling heartbeat","pbx","1000","45CAPS","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","jfavoxip2002","asvox1.vivo.net.br","102"
"2026-11-13","NGN-BR-SPO-CO-SRR1","PBX_2003","PBX trunk group","access_pbx","10.22.255.226","Access_Media","Disabling heartbeat","pbx","500","37CAPS","NULL","PLC_SET_ID_TGRP_Rmv_TG","SIP","SIP","DefaultPolicy","Yes","rjovoxip2003","asvox1.vivo.net.br","103"
```

## 6. Consulta Huawei SBCs

Mesma logica do SRR.

Arquivo `getISIPTG_SBC.bat`:

```bat
:: filepath: C:\Users\G0052677\Documents\Code\SRR\getISIPTG_SBC.py
@echo off
start "Monitor Arquivos SBC" plink.exe -ssh capacidade@10.200.35.226 -pw capacidade -t -no-antispoof "stty cols 140 rows 40 && cd /home/capacidade/SBC/HUAWEI/SBC && watch -n 0 ls -l"
cd /d "C:\Users\G0052677\Documents\Code\SRR"
start /b & "C:\Program Files\Python312\python.exe" c:/Users/G0052677/Documents/Code/SRR/getISIPTG_SBC.py
pause
```

Arquivo `getISIPTG_SBC.py`:

```python
#!/usr/bin/env python3
# -*- coding: utf-8 -*-
import socket
import time
import logging
import threading
from pathlib import Path
from datetime import datetime
from concurrent.futures import ThreadPoolExecutor, as_completed
import paramiko
# ==========================================================
# CONFIG
# ==========================================================
HOST = "201.69.136.99"
PORT = 31114
USER = "G0052677"
PASSWORD = "[Gvt]1404"

NES = [
    "MGSAG_VOXVMH_SBC01",
    "PRCTA_VOXVMH_SBC01",
    "RJBAR_VOXVMH_SBC4",
    "SPCON_VOXVMH_SBC8",
    "SPIBR_VOXVMH_SBC8",
    "SPPDZ_VOXVMH_SBC7",
    "RSIND-VOXVMH-SBC01",
    "NGN-BR-CTA-CTA-SBC1",
    "NGN-BR-RJO-BAR-SBC3",
    "NGN-BR-RJO-MSJ-SBC3",
    "NGN-BR-SPO-CO-SBC7",
    "NGN-BR-SPO-IB-SBC7",
    "NGN-BR-SPO-MB-SBC3",
    "NGN-BR-SPO-PA-SBC1",
    "NGN-BR-SPO-PD-SBC3",
    "DFTCO_IMSHUA_SBC01",
]

SFTP_HOST = "10.200.35.226"
SFTP_PORT = 22
SFTP_USER = "capacidade"
SFTP_PASSWORD = "capacidade"
DESTINO_SFTP = "/home/capacidade/SBC/HUAWEI/SBC"
BASE_DIR = Path(__file__).resolve().parent
MAX_WORKERS = 10

# ==========================================================
# CONTROLE DE CONSOLE
# ==========================================================

print_lock = threading.Lock()
contador_lock = threading.Lock()
concluidas = 0
total_nes = len(NES)

def log_console(msg):
    with print_lock:
        print(msg, flush=True)

# ==========================================================
# LOG
# ==========================================================

logging.basicConfig(
    filename=BASE_DIR / "coleta_huawei.log",
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(message)s"
)

# ==========================================================
# SOCKET
# ==========================================================

def conectar():
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.settimeout(10)
    logging.info(f"Conectando {HOST}:{PORT}")
    sock.connect((HOST, PORT))
    return sock

def executar(sock, comando, timeout=5):
    logging.info(comando)
    sock.sendall((comando + "\r\n").encode())
    resposta = ""
    fim = time.time() + timeout
    while time.time() < fim:
        try:
            dados = sock.recv(8192)
            if dados:
                texto = dados.decode("utf-8", errors="ignore")
                resposta += texto
                fim = time.time() + timeout
        except socket.timeout:
            pass
    return resposta

# ==========================================================
# ARQUIVOS
# ==========================================================

def salvar_log(ne, conteudo):
    ts = datetime.now().strftime("%Y-%m-%d_%H%M%S")
    arquivo = BASE_DIR / f"{ts}_{ne}.log"
    with open(arquivo, "w", encoding="utf-8") as f:
        f.write(conteudo)
    logging.info(f"Arquivo gerado {arquivo}")
    return arquivo

# ==========================================================
# SFTP
# ==========================================================

def upload_sftp(arquivo):
    try:
        transport = paramiko.Transport((SFTP_HOST, SFTP_PORT))
        transport.connect(
            username=SFTP_USER,
            password=SFTP_PASSWORD
        )
        sftp = paramiko.SFTPClient.from_transport(transport)
        remoto = f"{DESTINO_SFTP}/{arquivo.name}"
        sftp.put(str(arquivo),remoto)
        sftp.close()
        transport.close()
        log_console(f"[UPLOAD OK] {arquivo.name}")
    except Exception as e:
        logging.exception(f"Falha upload {arquivo}")
        log_console(f"[UPLOAD ERRO] {arquivo.name} -> {e}")
        raise

def executar_du():
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(
        hostname=SFTP_HOST,
        username=SFTP_USER,
        password=SFTP_PASSWORD
    )
    stdin, stdout, stderr = ssh.exec_command(f"du -ha {DESTINO_SFTP}")
    resultado = stdout.read().decode()
    log_console("\n===== DU -HA =====")
    log_console(resultado)
    logging.info(resultado)
    ssh.close()

# ==========================================================
# PROCESSAMENTO DA NE
# ==========================================================

def processar_ne(ne):
    global concluidas
    inicio = time.time()
    log_console(f"[INICIANDO] {ne}")
    sock = None
    try:
        sock = conectar()
        executar(sock, f'LGI:OP="{USER}",PWD="{PASSWORD}";')
        executar(sock, f'REG NE:NAME="{ne}";')
        resposta = executar(sock, "LST ISIPTG:;", timeout=20)
        arquivo = salvar_log(ne, resposta)
        executar(sock, f'UNREG NE:NAME="{ne}";')
        tempo = round(time.time() - inicio, 1)
        with contador_lock:
            concluidas += 1
            progresso = (f"[{concluidas}/{total_nes}]")
        log_console(f"{progresso} FINALIZADO {ne} ({tempo}s)")
        return arquivo
    except Exception as e:
        log_console(f"[ERRO] {ne} -> {e}")
        logging.exception(f"Erro NE {ne}")
        return None
    finally:
        try:
            if sock:
                sock.close()
        except:
            pass

# ==========================================================
# MAIN
# ==========================================================
def main():
    inicio_geral = time.time()
    log_console("")
    log_console("=" * 80)
    log_console(f"INICIANDO {total_nes} NEs EM PARALELO")
    log_console("=" * 80)
    arquivos = []
    with ThreadPoolExecutor(max_workers=MAX_WORKERS) as executor:
        futures = {
            executor.submit(processar_ne, ne): ne
            for ne in NES
        }
        log_console(f"Todas as {len(futures)} consultas foram disparadas.")
        for future in as_completed(futures):
            ne = futures[future]
            try:
                arquivo = future.result()
                if arquivo:
                    arquivos.append(arquivo)
            except Exception as e:
                log_console(f"[FALHA] {ne}: {e}")
    log_console("")
    log_console("=" * 80)
    log_console(f"CONSULTAS FINALIZADAS - {len(arquivos)} arquivos gerados")
    log_console("=" * 80)
    log_console("")
    log_console("Iniciando uploads SFTP...")
    log_console("")
    with ThreadPoolExecutor(max_workers=5) as executor:
        futures = [
            executor.submit(upload_sftp, arquivo)
            for arquivo in arquivos
        ]
        for future in as_completed(futures):
            future.result()
    executar_du()
    tempo_total = round(time.time() - inicio_geral, 1)
    log_console("")
    log_console("=" * 80)
    if tempo_total < 60:
        msg_tempo = f"PROCESSO FINALIZADO EM {tempo_total:.0f}s"
    else:
        minutos = int(tempo_total // 60)
        segundos = int(tempo_total % 60)
        msg_tempo = f"PROCESSO FINALIZADO EM {minutos}min{segundos}s"    

    log_console(msg_tempo)
    log_console("=" * 80)
    logging.info(msg_tempo)

if __name__ == "__main__":
    main()
```

Um programa chamado `watch_sbc.sh` que fica de olho nos arquivos novos que entram no diretorio:\
`/home/capacidade/SBC/HUAWEI/SBC`
Se tiver arquivos novos ele mais tres programas um para fazer parser, outro para fazer um filtro e outro para carregar o resultado na base. 

`parser_isiptg.sh`
`filtra_csv.sh`
`loadBase.sh`


Arquivo `watch_sbc.sh` :

```bash
#!/bin/bash
DIR="/home/capacidade/SBC/HUAWEI/SBC"
INTERVAL=30
cd "$DIR" || exit 1
echo "$(date '+%F %T') - Monitor iniciado"
while true
do
    FILES=(*SBC*.log)
    if [ ! -e "${FILES[0]}" ]; then
        sleep "$INTERVAL"
        continue
    fi
    QTD=$(ls -1 *SBC*.log 2>/dev/null | wc -l)
    if [ "$QTD" -ge 2 ]; then
        F1=$(ls -1 *SBC*.log | head -1)
        F2=$(ls -1 *SBC*.log | head -2 | tail -1)
        CHAVE="$F1|$F2"
        if [ "$CHAVE" != "$ULTIMO_PROCESSAMENTO" ]; then
            echo
            echo "$(date '+%F %T') - Encontrados:"
            echo "  $F1"
            echo "  $F2"
            echo "$(date '+%F %T') - Executando parser_isiptg.sh"
            ./parser_isiptg.sh
            CSV=$(ls -1t base_*.csv 2>/dev/null | head -1)
            if [ -n "$CSV" ]; then
                echo "$(date '+%F %T') - Executando filtra_csv.sh $CSV"
                ./filtra_csv.sh "$CSV"
                CSV_LIGHT="${CSV%.csv}_light.csv"
                if [ -f "$CSV_LIGHT" ]; then
                    echo "$(date '+%F %T') - Executando loadBase.sh $CSV_LIGHT"
                    ./loadBase.sh "$CSV_LIGHT"
                fi
            fi
            ULTIMO_PROCESSAMENTO="$CHAVE"
            echo "$(date '+%F %T') - Processamento concluído"
        fi
    fi
    sleep "$INTERVAL"
done
```

Arquivo `parser_isiptg.sh`: 

```bash
#!/bin/bash
OUTFILE="base_$(date +%F_%H%M%S).csv"
gawk '
###############################################################################
# CSV escape
###############################################################################
function csv(x) {
    gsub(/"/,"\"\"",x)
    return "\"" x "\""
}
###############################################################################
# Trim
###############################################################################
function trim(x) {
    sub(/^[ \t\r\n]+/, "", x)
    sub(/[ \t\r\n]+$/, "", x)
    return x
}
###############################################################################
# Descobre posições das colunas a partir do cabeçalho
###############################################################################
function build_columns(line,   i,c,start,len,name,colcount) {
    delete COL_START
    delete COL_END
    delete COL_NAME
    colcount = 0
    start = 0
    for (i=1; i<=length(line); i++) {
        c = substr(line,i,1)
        if (c != " " && (i==1 || substr(line,i-1,1)==" ")) {
            start=i
            while (i <= length(line) &&
                   !(substr(line,i,1)==" " &&
                     substr(line,i+1,1)==" ")) {
                i++
            }
            len=i-start
            name=trim(substr(line,start,len))
            if (length(name)>0) {
                colcount++
                COL_NAME[colcount]=name
                COL_START[colcount]=start
            }
        }
    }
    for(i=1;i<colcount;i++)
        COL_END[i]=COL_START[i+1]-1
    COL_END[colcount]=99999
    return colcount
}
###############################################################################
# Emite CSV
###############################################################################
function emit_record(line,   i,val) {
    if (trim(line)=="")
        return
    printf "%s,%s",
           csv(FILE_DATE),
           csv(FILE_SBC)
    for(i=1;i<=NCOLS;i++) {
        val=substr(line,
                   COL_START[i],
                   COL_END[i]-COL_START[i]+1)
        val=trim(val)
        printf ",%s",csv(val)
    }
    printf "\n"
}
BEGIN{
    header_written=0
    in_table=0
}
###############################################################################
# Captura SBC e Data
###############################################################################
/^\+\+\+/ {
    if (match($0,/MENAME:([^*]+)/))
        FILE_SBC=substr($0,RSTART+7,RLENGTH-7)

    if (match($0,/[0-9]{4}-[0-9]{2}-[0-9]{2}/))
        FILE_DATE=substr($0,RSTART,10)
    next
}
###############################################################################
# Detecta cabeçalho principal
###############################################################################
/Trunk group name/ && /TG ID/ {
    NCOLS=build_columns($0)
    if (!header_written) {
        printf "\"Data\",\"SBC\""
        for(i=1;i<=NCOLS;i++)
            printf ",%s",csv(COL_NAME[i])
        printf "\n"
        header_written=1
    }
    in_table=1
    next
}
###############################################################################
# Fim de página Huawei
###############################################################################
/^To be continued/ { next }
/^---[ ]*END/      { next }
###############################################################################
# Ignorar controle
###############################################################################
/^RETCODE/ { next }
/^The result is as follows/ { next }
/^------------------------/ { next }
/^%%/ { next }
/^O&M/ { next }
/^$/ { next }
###############################################################################
# Dados
###############################################################################
in_table {
    line=$0
    if (line ~ /^\+\+\+/)
        next
    if (line ~ /^%%/)
        next
    if (line ~ /^RETCODE/)
        next
    if (line ~ /^The result is as follows/)
        next
    if (line ~ /^------------------------/)
        next
    if (trim(line)=="")
        next
    emit_record(line)
}
' *.log > "$OUTFILE"
echo
echo "CSV gerado:"
echo "  $OUTFILE"
echo
echo "Total de linhas:"
wc -l "$OUTFILE"
rm -f *SBC*.log
```

Arquivo `filtra_csv.sh`:

```bash
#!/bin/bash
mv /home/capacidade/SBC/HUAWEI/SBC/*_light.csv /home/capacidade/SBC/HUAWEI/SBC/bkup
ARQ="$1"
if [ -z "$ARQ" ]; then
    echo "Uso: $0 arquivo.csv"
    exit 1
fi
SAIDA="/home/capacidade/SBC/HUAWEI/SBC/${ARQ%.csv}_light.csv"
gawk -F',' '
BEGIN {
    OFS=","
    remove["Link information"]
    remove["Enable TCP fallback"]
    remove["Local port number"]
    remove["Peer IP address type"]
    remove["Peer IPv6 address"]
    remove["Peer port number"]
    remove["Priority of media IP address type"]
    remove["Message absence triggered detection"]
    remove["Trigger period"]
    remove["Delete CIC"]
    remove["CAC policy set name 3"]
    remove["SIP policy name"]
    remove["Bearer control policy name"]
    remove["Transcode capability name"]
    remove["Automatic response to OPTIONS"]
    remove["Country code"]
    remove["Send COLP request"]
    remove["Calling number address attribute"]
    remove["Called number address attribute"]
    remove["Encryption policy set name"]
    remove["Fallback MTU for TCP messages"]
    remove["List ignored ports for inbound trunk"]
    remove["Outgoing trunk route selection policy"]
    remove["Enable incoming trunk number change"]
    remove["Incoming trunk number change policy"]
    remove["Inbound trunk number change group name"]
    remove["Enable outgoing trunk number change"]
    remove["Outgoing trunk number change policy"]
    remove["Outbound trunk number change group name"]
    remove["Certificate subject domain"]
    remove["Set QoS threshold"]
    remove["Critical alarm threshold for packet loss rate(%)"]
    remove["Major alarm threshold for packet loss rate(%)"]
    remove["Critical alarm threshold for RTD(ms)"]
    remove["Major alarm threshold for RTD(ms)"]
    remove["Critical alarm threshold for jitter(ms)"]
    remove["Major alarm threshold for jitter(ms)"]
    remove["Critical alarm threshold for MOS"]
    remove["Major alarm threshold for MOS"]
    remove["Routing percentage(%)"]
    remove["Clearance time for QoS critical alarm threshold(s)"]
    remove["Hide incoming trunk group name"]
    remove["Timeout time(s)"]
    remove["Fault detection counts"]
    remove["Fault recovery duration(s)"]
    remove["Number policy type"]
    remove["Calling number policy"]
    remove["Calling number policy enabled"]
    remove["Incoming calling blacklist policy"]
    remove["Calling number group name"]
    remove["Calling number deny response code"]
    remove["Incoming calling whitelist usergroup name"]
    remove["Incoming calling blacklist usergroup name"]
    remove["Called number policy"]
    remove["Called number policy enabled"]
    remove["Called number group name"]
    remove["Called number deny response code"]
    remove["Incoming called whitelist usergroup name"]
    remove["Incoming called blacklist usergroup name"]
    remove["Header containing calling number"]
    remove["Managed object group"]
    remove["Transport value"]
    remove["Match peer port"]
    remove["Fallback MTU for static TCP messages"]
    remove["Clearmode ptime"]
    remove["Reserved1"]
    remove["Reserved2"]
    remove["Reserved3"]
    remove["Reserved4"]
    remove["Customize OPTIONS detection"]
    remove["Confirm check interval(s)"]
    remove["Fault check interval(s)"]
    remove["Check times from confirm to fault"]
    remove["Check times from fault to normal"]
    remove["Enable flexible routing"]
    remove["Flexible routing policy"]
    remove["Common IP address type"]
    remove["Preferred IP address type"]
    remove["SIP-I policy name"]
    remove["SIPI/SIPT Country code"]
    remove["Calling number permit policy"]
    remove["Calling number permit policy"]
    remove["NoCharge error code"]
    remove["Enable PSTN XML"]
    remove["Enable MCID XML"]
    remove["Enable CUG XML"]
    remove["Enable SIP-I policy for SIP trunk"]
    remove["Local device ID"]
    remove["Peer device type"]
    remove["Peer device ID"]
    remove["Peer domain name"]
    remove["SIP entity type"]
    remove["User aging time (s)"]
    remove["Authentication"]
    remove["Authentication realm"]
    remove["User name for authentication"]
    remove["Authentication password"]
    remove["P-A1TA-Previous-Carrier header parameter"]
    remove["Enable Rf charging for TG"]
    remove["Access network information"]
    remove["Area code"]
    remove["CIC process policy"]
    remove["Corporate name"]
    remove["SIP recording policy"]
    remove["Virtual MGW policy name"]
    remove["Domain name hiding"]
    remove["TGRP parameter changing"]
    remove["Out trunk domain type"]
    remove["Enable video gateway fast-slow start conversion"]
    remove["Enable mobile codec strong selection function"]
}
NR==1 {
    for(i=1;i<=NF;i++) {
        h=$i
        gsub(/^"/,"",h)
        gsub(/"$/,"",h)
        if(!(h in remove)) {
            keep[i]=1
            out[++nout]=i
        }
        if(h=="Peer IPv4 address")
            ipcol=i
    }
    for(j=1;j<=nout;j++) {
        if(j>1) printf OFS
        printf "%s", $(out[j])
    }
    printf "\n"
    next
}
{
    ip=$ipcol
    gsub(/^"/,"",ip)
    gsub(/"$/,"",ip)
    if(ip=="")
        next
    for(j=1;j<=nout;j++) {
        if(j>1) printf OFS
        printf "%s", $(out[j])
    }
    printf "\n"
}
' "$ARQ" > "$SAIDA"
echo
echo "Arquivo original : $ARQ"
echo "Arquivo reduzido : $SAIDA"
echo
wc -l "$SAIDA"
rm -f /home/capacidade/SBC/HUAWEI/SBC/$ARQ
```

Arquivo `loadBase.sh`:

```bash
#!/bin/bash

ARQ=$(ls -1t /home/capacidade/SBC/HUAWEI/SBC/base*_light.csv | head -1)

printf "Uploading to [ tgrpSbcHuawei database ]...\n"
mysql -uroot -pEngCX666 --local_infile=1 dbengcx \
-e "LOAD DATA LOCAL INFILE '$ARQ' INTO TABLE tgrpSbcHuawei FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '\"' IGNORE 1 LINES";
mysql -uroot -pEngCX666 --l
```
exemplo:
`base_2026-11-13_075104_light.csv`

```bash
>head base_2026-11-13_075104_light.csv 
"Data","SBC","Trunk group name","Trunk group type","Local address name","Peer IPv4 address","Media domain name","Heartbeat detection","Inbound trunk route name","CAC policy set name 1","CAC policy set name 2","Inbound HMR policy set ID","Outbound HMR policy set ID","SIP inbound trunk signaling type","SIP outbound trunk signaling type","Add TGRP","Trunk-group parameter","Trunk-context parameter","TG ID"
"2026-11-13","MGSAG_VOXVMH_SBC01","A_3CORP_CN2_3_6X_LCTLC","Common trunk group","A_3CORP_CN2_3_6X_LCTLC","10.125.1.14","A_MEDIA_3CORP","Heuristic heartbeat","TO_ASITX_RJ_MG","390MAX","13CAPS","REMOVE_IDENTITY","PLC_RMV_TGRP_TG_ITX","SIP","SIP","Yes","X.3CO.RJMG.LC.VOXMG","X.55750.STFC","143"
"2026-11-13","MGSAG_VOXVMH_SBC01","A_3CORP_CN2_3_6X_LDN15","Common trunk group","A_3CORP_CN2_3_6X_LDN15","10.125.1.26","A_MEDIA_3CORP","Heuristic heartbeat","TO_ASITX_RJ_MG","180MAX","6CAPS","REMOVE_IDENTITY","PLC_RMV_TGRP_TG_ITX","SIP","SIP","Yes","X.3CO.RJMG.LD15.VOXMG","X.55750.STFC","144"
"2026-11-13","MGSAG_VOXVMH_SBC01","A_3CORP_CN2_3_6X_LDSCP","Common trunk group","A_3CORP_CN2_3_6X_LDSCP","10.125.1.18","A_MEDIA_3CORP","Heuristic heartbeat","TO_ASITX_RJ_MG","120MAX","4CAPS","REMOVE_IDENTITY","PLC_RMV_TGRP_TG_ITX","SIP","SIP","Yes","X.3CO.RJMG.LDOP.VOXMG","X.55750.STFC","145"
"2026-11-13","MGSAG_VOXVMH_SBC01","A_3CORP_CN2_3_6X_VC1","Common trunk group","A_3CORP_CN2_3_6X_VC1","10.125.1.22","A_MEDIA_3CORP","Heuristic heartbeat","TO_ASITX_SMP_RJ_MG","150MAX","4CAPS","REMOVE_IDENTITY","PLC_RMV_TGRP_TG_ITX","SIP","SIP","Yes","X.3CO.RJMG.VC1.VOXMG","X.55750.STFC","146"
"2026-11-13","MGSAG_VOXVMH_SBC01","A_AGERA_CN2_3_6X_LCTLC","Common trunk group","A_AGERA_CN2_3_6X_LCTLC","10.10.191.4","A_AGERA_RTP_MEDIA","Continuous heartbeat","TO_ASITX_RJ_MG","181MAX","12CAPS","REMOVE_IDENTITY","PLC_RMV_TGRP_TG_ITX","SIP","SIP","Yes","X.911.CN.LCTR.02.VXSAG","X.55191.STFC","157"
"2026-11-13","MGSAG_VOXVMH_SBC01","A_AGERA_CN2_3_6X_LDN15","Common trunk group","A_AGERA_CN2_3_6X_LDN15","10.10.191.4","A_AGERA_RTP_MEDIA","Continuous heartbeat","TO_ASITX_RJ_MG","90MAX","6CAPS","REMOVE_IDENTITY","PLC_RMV_TGRP_TG_ITX","SIP","SIP","Yes","X.911.CN.LD15.02.VXSAG","X.55191.STFC","160"
"2026-11-13","MGSAG_VOXVMH_SBC01","A_AGERA_CN2_3_6X_LD_CNG","Common trunk group","A_AGERA_CN2_3_6X_LD_CNG","10.10.191.4","A_AGERA_RTP_MEDIA","Continuous heartbeat","TO_ASITX_RJ_MG","206MAX","14CAPS","REMOVE_IDENTITY","PLC_RMV_TGRP_TG_ITX","SIP","SIP","Yes","X.911.CN.TRLD.02.VXSAG","X.55191.STFC","158"
"2026-11-13","MGSAG_VOXVMH_SBC01","A_AGERA_CN2_3_6X_VC1","Common trunk group","A_AGERA_CN2_3_6X_VC1","10.10.191.4","A_AGERA_RTP_MEDIA","Continuous heartbeat","TO_ASITX_SMP_RJ_MG","181MAX","12CAPS","REMOVE_IDENTITY","PLC_RMV_TGRP_TG_ITX","SIP","SIP","Yes","X.911.CN.VC1.02.VXSAG","X.55191.STFC","159"
"2026-11-13","MGSAG_VOXVMH_SBC01","A_BRDSF_BHECN31_LCTLC","Common trunk group","A_BRDSF_BHECN31_LCTLC","10.31.44.113","A_BRDSF_RTP_MEDIA","Heuristic heartbeat","TO_ASITX_RJ_MG","62MAX","2CAPS","REMOVE_IDENTITY","PLC_RMV_TGRP_TG_ITX","SIP","SIP","Yes","X.O85.CN31.LC.02.VXSA1","X.55711.STFC","73"
.
.
.
```

## 7. Consulta Huawei iBCFs

Mesma logica do SRR.

Arquivo `getiBCF.bat`:

```bat
:: filepath: C:\Users\G0052677\Documents\Code\SRR\getiBCF.py
@echo off
start "Monitor Arquivos SBC" plink.exe -ssh capacidade@10.200.35.226 -pw capacidade -t -no-antispoof "cd /home/capacidade/SBC/HUAWEI/BCF && watch -n 0 ls -l"
cd /d "C:\Users\G0052677\Documents\Code\SRR"
start /b & "C:\Program Files\Python312\python.exe" c:/Users/G0052677/Documents/Code/SRR/getiBCF.py
pause
```

Arquivo `getiBCF.py`: 

```python 
#!/usr/bin/env python3
# -*- coding: utf-8 -*-

import socket
import time
import logging
import threading
from pathlib import Path
from datetime import datetime
from concurrent.futures import ThreadPoolExecutor, as_completed
import paramiko
# ==========================================================
# CONFIG
# ==========================================================
HOST = "201.69.136.99"
PORT = 31114
USER = "G0052677"
PASSWORD = "[Gvt]1404"

NES = [

    "SPCON-IMSVMH-CSC02",
    "SPMRB-IMSVMH-CSC02",
    "SPCON-IMSVMH-CSC01",
    "SPMRB-IMSVMH-CSC01",    
    "SPCEN-IMSVMH-CSC01",
    "SPAFL-IMSVMH-CSC01",
    "RSIND-IMSVMH-CSC01",
    "AMAFP-IMSVMH-CSC01",
    "BATCS-IMSVMH-CSC01",
    "CEFLA-IMSVMH-CSC01",
    "DFTCO-IMSVMH-CSC01",
    "MGBHF-IMSVMH-CSC02",
    "MGBHF-IMSVMH-CSC01",
    "PACCP-IMSVMH-CSC01",
    "PRCTA-IMSVMH-CSC01",
    "RJBAR-IMSVMH-CSC01",
]

SFTP_HOST = "10.200.35.226"
SFTP_PORT = 22
SFTP_USER = "capacidade"
SFTP_PASSWORD = "capacidade"
DESTINO_SFTP = "/home/capacidade/SBC/HUAWEI/BCF"
BASE_DIR = Path(__file__).resolve().parent
MAX_WORKERS = 10

# ==========================================================
# CONTROLE DE CONSOLE
# ==========================================================

print_lock = threading.Lock()
contador_lock = threading.Lock()
concluidas = 0
total_nes = len(NES)

def log_console(msg):
    with print_lock:
        print(msg, flush=True)

# ==========================================================
# LOG
# ==========================================================

logging.basicConfig(
    filename=BASE_DIR / "coleta_huawei.log",
    level=logging.INFO,
    format="%(asctime)s %(levelname)s %(message)s"
)

# ==========================================================
# SOCKET
# ==========================================================

def conectar():
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.settimeout(10)
    logging.info(f"Conectando {HOST}:{PORT}")
    sock.connect((HOST, PORT))
    return sock

def executar(sock, comando, timeout=5):
    logging.info(comando)
    sock.sendall((comando + "\r\n").encode())
    resposta = ""
    fim = time.time() + timeout
    while time.time() < fim:
        try:
            dados = sock.recv(8192)
            if dados:
                texto = dados.decode("utf-8", errors="ignore")
                resposta += texto
                fim = time.time() + timeout
        except socket.timeout:
            pass
    return resposta

# ==========================================================
# ARQUIVOS
# ==========================================================

def salvar_log(ne, conteudo):
    ts = datetime.now().strftime("%Y-%m-%d_%H%M%S")
    arquivo = BASE_DIR / f"{ts}_{ne}.log"
    with open(arquivo, "w", encoding="utf-8") as f:
        f.write(conteudo)
    logging.info(f"Arquivo gerado {arquivo}")
    return arquivo

# ==========================================================
# SFTP
# ==========================================================

def upload_sftp(arquivo):
    try:
        transport = paramiko.Transport((SFTP_HOST, SFTP_PORT))
        transport.connect(
            username=SFTP_USER,
            password=SFTP_PASSWORD
        )
        sftp = paramiko.SFTPClient.from_transport(transport)
        remoto = f"{DESTINO_SFTP}/{arquivo.name}"
        sftp.put(str(arquivo),remoto)
        sftp.close()
        transport.close()
        log_console(f"[UPLOAD OK] {arquivo.name}")
    except Exception as e:
        logging.exception(f"Falha upload {arquivo}")
        log_console(f"[UPLOAD ERRO] {arquivo.name} -> {e}")
        raise

def executar_du():
    ssh = paramiko.SSHClient()
    ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
    ssh.connect(
        hostname=SFTP_HOST,
        username=SFTP_USER,
        password=SFTP_PASSWORD
    )
    stdin, stdout, stderr = ssh.exec_command(f"du -ha {DESTINO_SFTP}")
    resultado = stdout.read().decode()
    log_console("\n===== DU -HA =====")
    log_console(resultado)
    logging.info(resultado)
    ssh.close()

# ==========================================================
# PROCESSAMENTO DA NE
# ==========================================================

def processar_ne(ne):
    global concluidas
    inicio = time.time()
    log_console(f"[INICIANDO] {ne}")
    sock = None
    try:
        sock = conectar()
        executar(sock, f'LGI:OP="{USER}",PWD="{PASSWORD}";')
        executar(sock, f'REG NE:NAME="{ne}";')
        resposta = executar(sock, "LST SIPTG:;", timeout=20)
        arquivo = salvar_log(ne, resposta)
        executar(sock, f'UNREG NE:NAME="{ne}";')
        tempo = round(time.time() - inicio, 1)
        with contador_lock:
            concluidas += 1
            progresso = (f"[{concluidas}/{total_nes}]")
        log_console(f"{progresso} FINALIZADO {ne} ({tempo}s)")
        return arquivo
    except Exception as e:
        log_console(f"[ERRO] {ne} -> {e}")
        logging.exception(f"Erro NE {ne}")
        return None
    finally:
        try:
            if sock:
                sock.close()
        except:
            pass

# ==========================================================
# MAIN
# ==========================================================

def main():
    inicio_geral = time.time()
    log_console("")
    log_console("=" * 80)
    log_console(f"INICIANDO {total_nes} NEs EM PARALELO")
    log_console("=" * 80)
    arquivos = []
    with ThreadPoolExecutor(max_workers=MAX_WORKERS) as executor:
        futures = {
            executor.submit(processar_ne, ne): ne
            for ne in NES
        }
        log_console(f"Todas as {len(futures)} consultas foram disparadas.")
        for future in as_completed(futures):
            ne = futures[future]
            try:
                arquivo = future.result()
                if arquivo:
                    arquivos.append(arquivo)
            except Exception as e:
                log_console(f"[FALHA] {ne}: {e}")
    log_console("")
    log_console("=" * 80)
    log_console(f"CONSULTAS FINALIZADAS - {len(arquivos)} arquivos gerados")
    log_console("=" * 80)
    log_console("")
    log_console("Iniciando uploads SFTP...")
    log_console("")
    with ThreadPoolExecutor(max_workers=5) as executor:
        futures = [
            executor.submit(upload_sftp, arquivo)
            for arquivo in arquivos
        ]
        for future in as_completed(futures):
            future.result()
    executar_du()
    tempo_total = round(time.time() - inicio_geral, 1)
    log_console("")
    log_console("=" * 80)
    if tempo_total < 60:
        msg_tempo = f"PROCESSO FINALIZADO EM {tempo_total:.0f}s"
    else:
        minutos = int(tempo_total // 60)
        segundos = int(tempo_total % 60)
        msg_tempo = f"PROCESSO FINALIZADO EM {minutos}min{segundos}s"    

    log_console(msg_tempo)
    log_console("=" * 80)
    logging.info(msg_tempo)

if __name__ == "__main__":
    main()
```

Um programa chamado `watch_ibcf.sh` que fica de olho nos arquivos novos que entram no diretorio:\
`/home/capacidade/SBC/HUAWEI/BCF`
Se tiver arquivos novos ele mais tres programas um para fazer parser, outro para fazer um filtro e outro para carregar o resultado na base. 

`parser_isiptg.sh`\
`filtra_csv.sh`\
`loadBase.sh`

Arquivo `watch_ibcf.sh`:

```bash
#!/bin/bash
DIR="/home/capacidade/SBC/HUAWEI/BCF"
INTERVAL=30
cd "$DIR" || exit 1
echo "$(date '+%F %T') - Monitor iniciado"
while true
do
    FILES=(*CSC*.log)
    if [ ! -e "${FILES[0]}" ]; then
        sleep "$INTERVAL"
        continue
    fi
    QTD=$(ls -1 *CSC*.log 2>/dev/null | wc -l)
    if [ "$QTD" -ge 2 ]; then
        F1=$(ls -1 *CSC*.log | head -1)
        F2=$(ls -1 *CSC*.log | head -2 | tail -1)
        CHAVE="$F1|$F2"
        if [ "$CHAVE" != "$ULTIMO_PROCESSAMENTO" ]; then
            echo
            echo "$(date '+%F %T') - Encontrados:"
            echo "  $F1"
            echo "  $F2"
            echo "$(date '+%F %T') - Executando parser_isiptg.sh"
            ./parser_isiptg.sh
            CSV=$(ls -1t base_*.csv 2>/dev/null | head -1)
            if [ -n "$CSV" ]; then
                echo "$(date '+%F %T') - Executando filtra_csv.sh $CSV"
                ./filtra_csv.sh "$CSV"
                CSV_LIGHT="${CSV%.csv}_light.csv"
                if [ -f "$CSV_LIGHT" ]; then
                    echo "$(date '+%F %T') - Executando loadBase.sh $CSV_LIGHT"
                    ./loadBase.sh "$CSV_LIGHT"
                fi
            fi
            ULTIMO_PROCESSAMENTO="$CHAVE"
            echo "$(date '+%F %T') - Processamento concluído"
        fi
    fi
    sleep "$INTERVAL"
done
```

Arquivo `parser_isiptg.sh`:

```bash
#!/bin/bash

OUTFILE="base_$(date +%F_%H%M%S).csv"

gawk '
###############################################################################
# CSV escape
###############################################################################
function csv(x) {
    gsub(/"/,"\"\"",x)
    return "\"" x "\""
}

###############################################################################
# Trim
###############################################################################
function trim(x) {
    sub(/^[ \t\r\n]+/, "", x)
    sub(/[ \t\r\n]+$/, "", x)
    return x
}

###############################################################################
# Descobre posições das colunas a partir do cabeçalho
###############################################################################
function build_columns(line,   i,c,start,len,name,colcount) {

    delete COL_START
    delete COL_END
    delete COL_NAME

    colcount = 0
    start = 0

    for (i=1; i<=length(line); i++) {

        c = substr(line,i,1)

        if (c != " " && (i==1 || substr(line,i-1,1)==" ")) {
            start=i

            while (i <= length(line) &&
                   !(substr(line,i,1)==" " &&
                     substr(line,i+1,1)==" ")) {
                i++
            }

            len=i-start

            name=trim(substr(line,start,len))

            if (length(name)>0) {
                colcount++
                COL_NAME[colcount]=name
                COL_START[colcount]=start
            }
        }
    }

    for(i=1;i<colcount;i++)
        COL_END[i]=COL_START[i+1]-1

    COL_END[colcount]=99999

    return colcount
}

###############################################################################
# Emite CSV
###############################################################################
function emit_record(line,   i,val) {

    if (trim(line)=="")
        return

    printf "%s,%s",
           csv(FILE_DATE),
           csv(FILE_BCF)

    for(i=1;i<=NCOLS;i++) {

        val=substr(line,
                   COL_START[i],
                   COL_END[i]-COL_START[i]+1)

        val=trim(val)

        printf ",%s",csv(val)
    }

    printf "\n"
}

BEGIN{
    header_written=0
    in_table=0
}

###############################################################################
# Captura BCF e Data
###############################################################################
/^\+\+\+/ {

    if (match($0,/MENAME:([^*]+)/))
        FILE_BCF=substr($0,RSTART+7,RLENGTH-7)

    if (match($0,/[0-9]{4}-[0-9]{2}-[0-9]{2}/))
        FILE_DATE=substr($0,RSTART,10)

    next
}

###############################################################################
# Detecta cabeçalho principal
###############################################################################
/Trunk group ID/ {

    NCOLS=build_columns($0)

    if (!header_written) {

        printf "\"Data\",\"BCF"

        for(i=1;i<=NCOLS;i++)
            printf ",%s",csv(COL_NAME[i])

        printf "\n"

        header_written=1
    }

    in_table=1
    next
}

###############################################################################
# Fim de página Huawei
###############################################################################
/^To be continued/ { next }
/^---[ ]*END/      { next }

###############################################################################
# Ignorar controle
###############################################################################
/^RETCODE/ { next }
/^The result is as follows/ { next }
/^------------------------/ { next }
/^%%/ { next }
/^O&M/ { next }
/^$/ { next }

###############################################################################
# Dados
###############################################################################
in_table {

    line=$0

    if (line ~ /^\+\+\+/)
        next

    if (line ~ /^%%/)
        next

    if (line ~ /^RETCODE/)
        next

    if (line ~ /^The result is as follows/)
        next

    if (line ~ /^------------------------/)
        next

    if (trim(line)=="")
        next

    emit_record(line)
}
' *.log > "$OUTFILE"

echo
echo "CSV gerado:"
echo "  $OUTFILE"
echo
echo "Total de linhas:"
wc -l "$OUTFILE"

rm -f *CSC*.log
```


Arquivo `filtra_csv.sh`:


```bash
#!/bin/bash
mv /home/capacidade/SBC/HUAWEI/BCF/*_light.csv /home/capacidade/SBC/HUAWEI/BCF/bkup
ARQ="$1"

if [ -z "$ARQ" ]; then
    echo "Uso: $0 arquivo.csv"
    exit 1
fi

SAIDA="/home/capacidade/SBC/HUAWEI/BCF/${ARQ%.csv}_light.csv"

gawk -F',' '
BEGIN {
    OFS=","

    remove["Local address ID"]
    remove["Trunk group type"]
    remove["Enable dynamic peer IP for trunk"]
    remove["Peer support registration"]
    remove["Registration identity of PBX"]
    remove["Address type"]
    remove["Media domain name"]
    remove["Enable routing information hiding"]
    remove["Require register"]
    remove["Caller replacement policy"]
    remove["PBX default caller"]
    remove["SIP status code for rejection"]
    remove["Alarm for trunk fault"]
    remove["Enable fast registration"]
    remove["Access side fast registration period"]
    remove["Core side fast registration expires"]
    remove["Maximum simultaneous sessions"]
    remove["Maximum simultaneous incoming sessions"]
    remove["Maximum simultaneous outgoing sessions"]
    remove["BCF bearer control policy ID"]
    remove["Enable signal policy of incoming trunk"]
    remove["Enable trusting route header"]
    remove["Index of incoming trunk"]
    remove["Number conversion index of incoming trunk"]
    remove["Country code"] 
    remove["Area code"]
    remove["Complete caller number"]
    remove["Incoming filter policy set name"]
    remove["Enable signal policy of outgoing trunk"]
    remove["Delete CIC"]
    remove["Index of route reselect policy"]
    remove["Index of SIP status code conversion policy"]
    remove["Not add route header"]
    remove["PBX outgoing header policy"]
    remove["Add RN parameter"]
    remove["Delete pbxid From P-Associated-URI"]
    remove["Process calling number for forwarded incoming calls"]
    remove["From conversion name of outgoing trunk"]
    remove["Emergency call policy"]
    remove["Enable replace the Request URI"]
    remove["Call authentication"] 
    remove["Reserved1"]
    remove["Reserved2"]
    remove["Reserved3"]
    remove["Reserved4"]
    remove["Reserved5"]
    remove["Incoming filter policy set ID"]
    remove["Outgoing filter policy set ID"]
}

NR==1 {

    for(i=1;i<=NF;i++) {

        h=$i
        gsub(/^"/,"",h)
        gsub(/"$/,"",h)

        if(!(h in remove)) {
            keep[i]=1
            out[++nout]=i
        }

        if(h=="Peer IP address")
            ipcol=i
    }

    for(j=1;j<=nout;j++) {
        if(j>1) printf OFS
        printf "%s", $(out[j])
    }
    printf "\n"

    next
}

{
    ip=$ipcol

    gsub(/^"/,"",ip)
    gsub(/"$/,"",ip)

    if(ip=="")
        next

    for(j=1;j<=nout;j++) {
        if(j>1) printf OFS
        printf "%s", $(out[j])
    }

    printf "\n"
}
' "$ARQ" > "$SAIDA"

echo
echo "Arquivo original : $ARQ"
echo "Arquivo reduzido : $SAIDA"
echo
wc -l "$SAIDA"

rm -f /home/capacidade/SBC/HUAWEI/BCF/$ARQ
```

Arquivo `loadBase.sh`:


```bash
#!/bin/bash
ARQ=$(ls -1t /home/capacidade/SBC/HUAWEI/BCF/base*_light.csv | head -1)

printf "Uploading to [ tgrpiBCF database ]...\n"
mysql -uroot -pEngCX666 --local_infile=1 dbengcx \
-e "LOAD DATA LOCAL INFILE '$ARQ' INTO TABLE tgrpiBCF FIELDS TERMINATED BY ',' OPTIONALLY ENCLOSED BY '\"' IGNORE 1 LINES";
mysql -uroot -pEngCX666 --local_infile=1 dbengcx -e "DELETE FROM tgrpiBCF WHERE data IS NULL";
printf "Finalizado OK.\n"
```

Exemplo:
`base_2026-11-13_080225_light.csv`


```bash
>cat base_2026-11-12_141014_light.csv | awk -F',' '!visto[$2]++'
"Data","BCF,"Trunk group ID","Peer IP address","Peer port number","Trunk group name","PBX identity","Inter operator identifier","Check heartbeat","Request-URI conversion name of outgoing trunk","P-Asserted-Identity conversion name of outgoing trunk","Outgoing filter policy set name","Return code of an unregistered user"
"2026-11-12","AMAFP-IMSVMH-CSC01","1400","10.123.2.135","5060","blm2VOXIP1400SIP","blm2voxip1400","1400","No","10.123.2.135_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS3-DOMAIN","NULL"
"2026-11-12","SPAFL-IMSVMH-CSC01","1111","10.229.96.21","5060","spoanvoxip4362","spoanvoxip4362","4362","No","10.229.96.21_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS4-DOMAIN","NULL"
"2026-11-12","SPCEN-IMSVMH-CSC01","1111","10.229.96.21","5060","spoanvoxip4362","spoanvoxip4362","4362","No","10.229.96.21_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS4-DOMAIN","NULL"
"2026-11-12","BATCS-IMSVMH-CSC01","1400","10.123.3.69","5060","sdr4VOXIP1400SIP","sdr4voxip1400","1400","No","10.123.3.69_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS3-DOMAIN","NULL"
"2026-11-12","RSIND-IMSVMH-CSC01","1400","10.118.21.51","5060","pae5VOXIP1400SIP","pae5voxip1400","1400","No","10.118.21.51_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS2-DOMAIN","NULL"
"2026-11-12","CEFLA-IMSVMH-CSC01","1400","10.123.3.69","5060","sdr4VOXIP1400SIP","sdr4voxip1400","1400","No","10.123.3.69_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS3-DOMAIN","NULL"
"2026-11-12","PACCP-IMSVMH-CSC01","1400","10.123.2.135","5060","blm2VOXIP1400SIP","blm2voxip1400","1400","No","10.123.2.135_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS3-DOMAIN","NULL"
"2026-11-12","MGBHF-IMSVMH-CSC01","1400","10.166.99.197","5060","bhe5VOXIP1400SIP","bhe5voxip1400","1400","No","10.166.99.197_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS3-DOMAIN","NULL"
"2026-11-12","PRCTA-IMSVMH-CSC01","1400","10.118.21.51","5060","pae5VOXIP1400SIP","pae5voxip1400","1400","No","10.118.21.51_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS2-DOMAIN","NULL"
"2026-11-12","DFTCO-IMSVMH-CSC01","1400","10.123.1.133","5060","bsa6VOXIP1400SIP","bsa6voxip1400","1400","No","10.123.1.133_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS2-DOMAIN","NULL"
"2026-11-12","MGBHF-IMSVMH-CSC02","1400","10.123.1.133","5060","bsa6VOXIP1400SIP","bsa6voxip1400","1400","No","10.123.1.133_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS2-DOMAIN","NULL"
"2026-11-12","RJBAR-IMSVMH-CSC01","1111","10.229.96.21","5060","spoanvoxip4362","spoanvoxip4362","4362","No","10.229.96.21_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS4-DOMAIN","NULL"
"2026-11-12","SPCON-IMSVMH-CSC01","1","10.229.96.21","5060","spopavoxip0001","spopavoxip0001","0001","No","10.229.96.21_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS4-DOMAIN","0"
"2026-11-12","SPMRB-IMSVMH-CSC01","1","10.229.96.21","5060","spopavoxip0001","spopavoxip0001","0001","No","10.229.96.21_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS4-DOMAIN","0"
"2026-11-12","SPMRB-IMSVMH-CSC02","2","10.229.94.21","5060","spocbvoxip3405","spocbvoxip3405","2","No","10.229.94.21_BCF2SBC_TEL2SIP","TEL2SIP_PAI_BCF2SBC","IP-PBX-DEL-TGID-ADD-IMS4-DOMAIN","0"
```


## 8. Coleta das 3 Bases para upload.


O programa que junta todas as bases e sobe para a base, roda num horario diferente por segurança em relação ao tempo que pode levar para terminar os outros scripts. 

Linha do arquivo crontab: [ adicionar com o comando `crontab -e`, comandos `'vi'` Teclas `<Win> + <Shift> + <i>` cola a linha aperta `<Esc>` escreve e sai com :wq! ]\
`0 4 * * * /home/capacidade/SBC/sbcLogs/consolidarBaseTgrp.sh >> /home/capacidade/SBC/sbcLogs/logDiario_consolidaTGRP.log 2>&1`

Diretório: `/home/capacidade/SBC/sbcLogs`



Arquivo `consolidarBaseTgrp.sh`:

```bash
#!/bin/bash

DIR1="/home/capacidade/SBC/GENBAND/tgrpRel/rest/sbcLogs"
DIR2="/home/capacidade/SBC/ACME/tgrpRel/rest/sbcLogs"
DIR3="/home/capacidade/SBC/SONUS/tgrpRel/rest/sbcLogs"

ONTEM=$(date -d "yesterday" +%Y%m%d)
DATA=$(date -d "yesterday" +%F)
TIMESTAMP=$(date -d "yesterday" +%Y%m%d_%H%M%S)

ARQ1=$(find "$DIR1" -maxdepth 1 -type f -name "base_${ONTEM}_*.csv" | sort | tail -1)
ARQ2=$(find "$DIR2" -maxdepth 1 -type f -name "base_${ONTEM}_*.csv" | sort | tail -1)
ARQ3=$(find "$DIR3" -maxdepth 1 -type f -name "base_${ONTEM}_*.csv" | sort | tail -1)

ERRO=0

[ -z "$ARQ1" ] && echo "Não encontrou arquivo do dia em $DIR1" && ERRO=1
[ -z "$ARQ2" ] && echo "Não encontrou arquivo do dia em $DIR2" && ERRO=1
[ -z "$ARQ3" ] && echo "Não encontrou arquivo do dia em $DIR3" && ERRO=1

[ $ERRO -eq 1 ] && exit 1

SAIDA="/home/capacidade/SBC/sbcLogs/baseConsolidada_${TIMESTAMP}.csv"

echo "data,sbc,realm,siptg,ip,canais,domain,piloto,fmm,routeContext,status,idAcme,endpointNameContext,ipsiptg,uPort,regId" > "$SAIDA"

#
# GENBAND
# 1 sbc
# 2 realm
# 3 domain
# 4 routeContext
# 5 siptg
# 6 siptg(rep)
# 7 routeContext(rep)
# 8 piloto
# 9 canais
#10 domain(rep)
#11 fmm
#12 ip
#13 endpointNameContext
#14 uPort
#15 regId
#
awk -F',' -v OFS=',' -v DATA="$DATA" '
{
    print DATA,
          $1,
          $2,
          $5,
          $12,
          $9,
          $3,
          $8,
          $11,
          $4,
          "",
          "",
          $13,
          "",
          $14,
          $15
}
' "$ARQ1" >> "$SAIDA"

#
# ACME
# 1 idAcme
# 2 sbc
# 3 ip
# 4 realm
# 5 ip(secundario - ignorado)
# 6 fmm
# 7 endpointNameContext
# 8 piloto
# 9 canais
#10 domain
#
awk -F',' -v OFS=',' -v DATA="$DATA" '
{
    print DATA,
          $2,
          $4,
          "",
          $3,
          $9,
          $10,
          $8,
          $6,
          "",
          "",
          $1,
          $7,
          "",
          "",
          ""
}
' "$ARQ2" >> "$SAIDA"

#
# SONUS
# 1 sbc
# 2 realm
# 3 siptg
# 4 status
# 5 canais
# 6 routeContext
# 7 domain
# 8 ipsiptg
# 9 dmPmRule -> piloto
#10 ip
#
awk -F',' -v OFS=',' -v DATA="$DATA" '
{
    print DATA,
          $1,
          $2,
          $3,
          $10,
          $5,
          $7,
          $9,
          "",
          $6,
          $4,
          "",
          "",
          $8,
          "",
          ""
}
' "$ARQ3" >> "$SAIDA"

echo ""
echo "=========================================="
echo "Consolidado gerado com sucesso"
echo "Arquivo: $SAIDA"
echo "GENBAND : $ARQ1"
echo "ACME    : $ARQ2"
echo "SONUS   : $ARQ3"
echo "=========================================="

printf "Uploading to [ tgrpSbc database ]...\n"
mysql -uroot -pEngCX666 --local_infile=1 dbengcx -e "LOAD DATA LOCAL INFILE '$SAIDA' INTO TABLE tgrpSbc FIELDS TERMINATED BY ','";
mysql -uroot -pEngCX666 --local_infile=1 dbengcx -e "DELETE FROM tgrpSbc WHERE data IS NULL";
printf "Finalizado OK./n"
```

Nome exemplo dos arquivos: `baseConsolidada_20261106_040001.csv`, a estrutura é adaptada para ter todas as informações dos 3 vendors. Consulta feita com filtro em SBCs específicos para ter o exemplo de vendors diferentes. 

```bash
>cat baseConsolidada_20261116_040001.csv | grep privaccess-voxip | egrep 'SBC1-MNS|ngn-br-vta-bf-sbc03|sbc10b-cta|sbc1a-mga' | grep -v ACCESS_UC_TG | awk -F',' '!visto[$2]++'
2026-11-16,sbc10b-cta,privaccess-voxip,cta7voxip12158,172.16.34.122,10,ims2.gvt.net.br,+554133013663,,ims2.gvt.net.br,,, DM COMERCIAL DE ENGRENAGENS E CORRENTES LTDA,,0,1-B7L2GXJDVC
2026-11-16,sbc1b-mga_sbc1a-mga,privaccess-voxip,,172.16.0.42,10,mga4voxip2063,<sip:+556734115300@ims2.gvt.net.br>,,,,ID55,1-90Z0VR4NVC@FRIGO DOURADOS ALIMENTOS,,,
2026-11-16,ngn-br-vta-bf-sbc03,privaccess-voxip,bhe5voxip1774,172.16.3.18,10,IMS3.GVT.NET.BR,voxip1774_2733572000,,voxip2,enabled,,,bhe5voxip1774_IPSIGP,,
2026-11-16,SBC1-MNS,privaccess-voxip,bhe4voxip2188,172.16.28.90,30,IMS3.GVT.NET.BR,voxip2188_6930120008,,voxip,enabled,,,bhe4voxip2188_IPSIGP,,
```

Arquivo de log. 


```bash
> tail -20 logDiario_consolidaTGRP.log
Uploading to [ tgrpSbc database ]...
Finalizado OK./n
==========================================
Consolidado gerado com sucesso
Arquivo: /home/capacidade/SBC/sbcLogs/baseConsolidada_20261115_040001.csv
GENBAND : /home/capacidade/SBC/GENBAND/tgrpRel/rest/sbcLogs/base_20261115_234110.csv
ACME    : /home/capacidade/SBC/ACME/tgrpRel/rest/sbcLogs/base_20261115_234001.csv
SONUS   : /home/capacidade/SBC/SONUS/tgrpRel/rest/sbcLogs/base_20261115_235410.csv
==========================================
Uploading to [ tgrpSbc database ]...
Finalizado OK./n
==========================================
Consolidado gerado com sucesso
Arquivo: /home/capacidade/SBC/sbcLogs/baseConsolidada_20261116_040001.csv
GENBAND : /home/capacidade/SBC/GENBAND/tgrpRel/rest/sbcLogs/base_20261116_234102.csv
ACME    : /home/capacidade/SBC/ACME/tgrpRel/rest/sbcLogs/base_20261116_234001.csv
SONUS   : /home/capacidade/SBC/SONUS/tgrpRel/rest/sbcLogs/base_20261116_235324.csv
==========================================
Uploading to [ tgrpSbc database ]...
```
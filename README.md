Uso via Embed - Plataforma Mitte Tecnologia
-----------------

**Fluxo:**
 - Solicitar a Mitte a URL da API, *AUTH_TOKEN* e o *ID do Evento* que será integrado;
 - Requisitar a API o acesso ao sistema enviando o e-mail (em), first name (fn), last name (ln), ticket name (tn), QR-Code (qr), CPF quando existir (cpf), RG quando existir (rg);
 - Após validação dos dados, a API retornará um link específico que deverá ser embedado via iframe. 


**Autenticação:**
 - Os dados de autenticação para cada API/Evento deverá ser solicitado ao Suporte Mitte; 
 - Exemplo de dados retornados pela Mitte:
   
   > **API:** https://dev.automacaodeeventos.com.br/demo/api/embed/  
   > **AUTH_TOKEN:** 3239e7e182a4c615785537b28611049d  
   > **EV:** 2
 - O *AUTH_TOKEN* deverá ser anexado no header de cada solicitação, exemplo:

    ```curl_setopt($curl, CURLOPT_HTTPHEADER, ['AUTH_TOKEN: 12345']);```

**Dados do Participante:**
  - Exceto CPF e RG, todos os dados são obrigatórios e devem ser enviados via POST.
  ``` 
    $data = {
    	"ev":"1",
    	"em":"email@email.com",
    	"fn":"Nome",
    	"ln":"Sobrenome",
    	"tn":"VIP",
    	"qr":"AFFA3I2GHJ",
    	"cpf":"05864954823",
    	"rg":""
    };
  ```
     
  >  `ev: ` Inteiro (11) - ID do evento - ***Required***  
  >  `em: ` String (60) - E-mail do participante - ***Required***  
  >  `fn: ` String (40) - Primeiro nome - ***Required***  
  >  `ln: ` String (80) - Último nome  - ***Required***  
  >  `tn: ` String (200) - Categoria/Tipo do ticket - ***Required***  
  >  `qr: ` String (20) - Código impresso no ingresso - ***Required***  
  >  `CPF: ` String (11) - CPF sem pontos e traços - *Optional*  
  >  `RG: ` String (20) - RG - *Optional* 

**Exemplo de Chamada** 

    $data = '{
    	"ev":"2",
    	"em":"email@email.com",
    	"fn":"Nome",
    	"ln":"Sobrenome",
    	"tn":"VIP",
    	"qr":"AFFA3I2GHJ",
    	"cpf":"05864954823",
    	"rg":""
    }';
    
    $curl = curl_init();
    curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
    curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
    curl_setopt($curl, CURLOPT_HTTP_VERSION, CURL_HTTP_VERSION_1_1);
    curl_setopt($curl, CURLOPT_URL, "https://dev.automacaodeeventos.com.br/demo/api/embed/");
    curl_setopt($curl, CURLOPT_POST, 1);
    curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
    curl_setopt($curl, CURLOPT_HTTPHEADER, ['AUTH_TOKEN: 3239e7e182a4c615785537b28611049d']);
    $resultado = curl_exec($curl);
    curl_close($curl);
    $dados = json_decode($resultado, JSON_UNESCAPED_UNICODE);

**Exemplo de Retorno** 

    {
        "status": 200,
        "code": 1,
        "message": "Integração Realizada",
        "embed": "https://dev.automacaodeeventos.com.br/demo/inscricao/val_login.asp?token=87858dc31666de5636efe5176b1e7421&eveId=1"
    }

**Exemplo de uso do Embed** 

    <iframe src="https://dev.automacaodeeventos.com.br/demo/inscricao/val_login.asp?token=87858dc31666de5636efe5176b1e7421&eveId=1" frameBorder="0" style="width:100%;height:100%;max-width:100vw;max-height:100vh; border: 0 !important; padding: 0; margin: 0"></iframe>

**Considerações Adicionais**

 - O token tem limite de 5 minutos de expiração e limitado a um único uso, só deve ser gerado no momento em que o cliente for acessar;
 - Em caso de erros no processamento das requisições, as seguintes situações são conhecidas:  

> **401 Unauthorized**: ausência de token, id do evento não enviado, token não habilitado no sistema; 
> 
>     {
>         "status": 401,
>         "error": true,
>         "code": 2,
>         "message": "Token inválido"
>     }

> **400 Bad Request**: dados ausentes, nome inválido, sobrenome inválido, categoria inválida; 
> 
> 
>     {
>         "status": 400,
>         "error": true,
>         "code": 3,
>         "message": "Nome inválido"
>     }


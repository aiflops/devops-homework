Zadanie polega na stworzeniu zestawu serwisow przy pomocy docker-compose.

Ogolny schemat aplikacji:

`haproxy` --> `backend1`
          \-> `backend2`

Gdzie haproxy moze byc gotowym, publicznym obrazem, ze zmodyfikowana konfiguracja obslugujaca:
  1. blokade ruchu przy dlugosci user agenta rownej lub mniejszej niz 16 znakow,
  2. liste zabronionych user agentow (ladowana z pliku),
  3. liste dozwolonych user agentow (override dla reguly nr. 1)
  4. backend skladajacy sie z dwoch serwerow `backend1` oraz `backend2`, gdzie `backend2` jest serwerem backupowym

BackendN to wlasnorecznie napisana aplikacja w Pythonie ktora obsluguje:
  1. endpoint http `/healthz` zwracajacy "HTTP 200 OK" na potrzeby healtcheckow z haproxy
  2. endpoint http `/` zwracajacy dowolna tresc, pozwalajaca odroznic kontener z ktorego jest serwowana

Odpytujac na lokalnej maszynie adres localhost:9090, powinnismy dostac output z backend1.
W przypadku jego zatrzymania (docker-compose stop backend1) haproxy powinno serwowac tresc z backend2.
Po ponownym podniesieniu backend1 ruch powinien znowu byc serwowany z niego.

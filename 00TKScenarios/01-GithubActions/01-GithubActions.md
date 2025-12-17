# GitHub Actions: szablon uruchomienia `azd pipeline config`

## Cel

Automatyzacja wdrazania projektu

## Warunki wstępne

- Pracujesz w GitHub Codespaces dla tego repozytorium.
- Własny fork repozytorium.
- EntraID - Application Developer
- Azure - contributor w resource group
- nazwa resource group do wdrozenia
- używamy raczej Sweden Central / West Europe
- subskrypcja to ME-MngEnvMCAP263417-edbartko-2 (589ff257-94b5-4699-bcdf-ce867938ac4d)


W Codespaces narzędzia są dostępne w kontenerze (m.in. `azd` i `az`). Jeśli chcesz to szybko sprawdzić:

```shell
azd version
az version
```

## Zaloguj się do Azure (z poziomu terminala w Codespaces):

```shell
azd auth login
```

Jeśli logowanie przez przeglądarkę nie zadziała lub zobaczysz komunikat o braku przeglądarki, użyj trybu device code:

![alt text](image.png)

![alt text](image-1.png)

(albo na wprost)

```shell
azd auth login --use-device-code
```

## Utwórz nowe środowisko azd (jedno na uczestnika/zespół):

```shell
azd env new
```

Nazwa środowiska to nazwa resource group.

![alt text](image-8.png)

Dodanie właściwiej resource group 
Bez tego probowac bedzie stworzyc zasoby w subskrypcji
Bez tego powstanie resource group bazujaca na nazwie srodowiska

```shell
azd env set AZURE_RESOURCE_GROUP rg-azureclubworkshopchat-50
```

main.bicep
targetScope = 'resourceGroup'

![alt text](image-10.png)

![alt text](image-9.png)

1. Uruchom jednorazowy deploy ręczny (wymagane przed pipeline):

```shell
azd up
```

![alt text](image-2.png)

![alt text](image-3.png)

![alt text](image-4.png)

![alt text](image-5.png)

![alt text](image-6.png)

Efekt:

![alt text](image-7.png)

## (opcjonalnie) Uruchom lokalnie

```shell
@tomasz_mhent ➜ /workspaces/tomasz-base-app (main) $ chmod +x ./app/start.sh
@tomasz_mhent ➜ /workspaces/tomasz-base-app (main) $ ./app/start.sh
```

![alt text](image-26.png)

![alt text](image-27.png)

![alt text](image-28.png) 

## Skonfiguruj GitHub Actions przez `azd pipeline config`:

User Access Administrator

```shell
azd pipeline config
```

![alt text](image-11.png)

![alt text](image-12.png)

Bo jestesmy w nowym work profile
![alt text](image-13.png)


![alt text](image-14.png)

![alt text](image-15.png) 

![alt text](image-16.png)

![alt text](image-17.png)

Wazne - 

![alt text](image-18.png)

SP + OIDC

![alt text](image-29.png)

![alt text](image-19.png)

![alt text](image-20.png)

![alt text](image-21.png)

![alt text](image-22.png)


ENABLE WORKFLOW ON FORK

![alt text](image-23.png)

Ręcznie:

![alt text](image-24.png)

Ważny krok:

![alt text](image-25.png) 

(Analiza skryptu "wgrywającego" i logika azd - pipeline)

## Notatki

- Workflow jest już w repo w pliku `.github/workflows/azure-dev.yml` i używa głównie GitHub **Variables** (nie Secrets) do konfiguracji.
- `azd pipeline config` powinno być uruchamiane po `azd up`, bo wtedy `azd` ma komplet wartości środowiska do zapisania w repo.
- Jeżeli hackathon używa innej gałęzi niż `main`/`master`, dostosuj `on.push.branches` w workflow lub uruchamiaj go ręcznie.


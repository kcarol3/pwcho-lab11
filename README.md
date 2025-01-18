# Sprawozdanie Laboratorium 11

---

## Odpowiedzi do zadania A:

### Deployment:
- Służy do zarządzania aplikacjami bezstanowymi (stateless).
- Skalowanie „w górę”: nowe repliki są tworzone jako niezależne od siebie i równoważne instancje.
- Skalowanie „w dół”: dowolne repliki mogą zostać usunięte.

### StatefulSet:
- Służy do zarządzania aplikacjami stanowymi (stateful), gdzie wymagane jest zachowanie tożsamości poszczególnych instancji np. takich jak bazy danych.
- Skalowanie „w górę”: nowe repliki są tworzone w kolejności, z zachowaniem unikalnych nazw.
- Skalowanie „w dół”: repliki są usuwane w odwrotnej kolejności (ostatnia tworzona replika jest usuwana jako pierwsza).

## **Struktura projektu**
### **1. Pliki wchodzące w skład zadania**
- **`db-secret.yaml`**  
  Zawiera definicję sekretu Kubernetes (`Secret`), przechowującego dane uwierzytelniające do bazy danych

- **`db-service.yaml`**  
  Definicja usługi (`Service`), umożliwiająca komunikację z bazą danych MySQL w klastrze Kubernetes

- **`db-stateful-setup.yaml`**  
  Manifest `StatefulSet` definiujący konfigurację aplikacji MySQL

---

## **Instrukcja uruchomienia**
1. **Uruchomienie sekretu**
   ```bash
   kubectl apply -f db-secret.yaml
   ```

2. **Uruchomienie usługi**
   ```bash
   kubectl apply -f db-service.yaml
   ```

3. **Wdrożenie StatefulSet**
   ```bash
   kubectl apply -f db-stateful-setup.yaml
   ```

4. **Sprawdzenie stanu podów i weryfikacja StatefulSet**
   ```bash
   kubectl get pods
   kubectl get statefulset
   
   
   macbookpro@MacBook-Pro-MacBook lab11 % kubectl get pods                       
    NAME      READY   STATUS    RESTARTS   AGE
    mysql-0   1/1     Running   0          25m
    mysql-1   1/1     Running   0          25m
    mysql-2   1/1     Running   0          25m
   ```
---

## **Testowanie**
1. **Sprawdzenie logów podu**
   ```bash
   kubectl describe pod mysql-0
   ```

2. **Skalowanie StatefulSet w dół**
   ```bash
   macbookpro@MacBook-Pro-MacBook lab11 % kubectl scale statefulset mysql --replicas=2
    statefulset.apps/mysql scaled
   ```
   Sprawdzenie podów:
   ```bash
   macbookpro@MacBook-Pro-MacBook lab11 % kubectl get pods
    NAME      READY   STATUS    RESTARTS   AGE
    mysql-0   1/1     Running   0          26m
    mysql-1   1/1     Running   0          26m
   ```
   Pody są usuwane zaczynając od najwyższego indeksu

3. **Skalowanie StatefulSet w górę**
   ```bash
   macbookpro@MacBook-Pro-MacBook lab11 % kubectl scale statefulset mysql --replicas=5
    statefulset.apps/mysql scaled
   ```
   Sprawdzenie podów:
   ```bash
   macbookpro@MacBook-Pro-MacBook lab11 % kubectl get pods                            
    NAME      READY   STATUS    RESTARTS   AGE
    mysql-0   1/1     Running   0          28m
    mysql-1   1/1     Running   0          28m
    mysql-2   1/1     Running   0          81s
    mysql-3   1/1     Running   0          79s
    mysql-4   1/1     Running   0          76s
   ```
---

## Пример pvc-cloning в minikube

Ограничения:
1. Клонирование работает только для CSI драйверов. 
Инструкцию по установке можно посмотреть на сайте [minikube](https://minikube.sigs.k8s.io/docs/tutorials/volume_snapshots_and_csi/)
2. Клонирование работает только с динамическими provisioner. PV создавать не нужно
3. Драйвер должен поддерживать клонирование. SCI-Hostpath драйвер для Minikube поддерживает его. 
Информацию по остальным драйверам можно посмотреть [тут](https://kubernetes-csi.github.io/docs/drivers.html)

Остальные ограничения можно посмотреть [тут](https://kubernetes.io/docs/concepts/storage/volume-pvc-datasource/#introduction)

### Инструкция по запуску

1. Создаем pvc, который будем клонировать
```shell
kubectl apply -f pvc-original.yaml
```

2. Проверяем что создался PVC и PV
```shell
kubectl get pvc
kubectl get pv
```
У PVC должен быть статус `Bound`

3. Создаем джобу, которая создаст файл в оригинальном хранилище:
```shell
kubectl apply -f job.yaml
```
Ждем пока выполнится джоба.

4. Создаем pod, который будет смотреть на оригинальный PVC
```shell
kubectl apply -f deployment.yaml
```
Ждем пока pod запустится.

5. Проверяем что файл есть в оригинальном хранилище
```shell
kubectl exec -it nginx-clone-{hash} -- ls -lah /home/main/100MB.bin
```

6. Создаем клон хранилища
```shell
kubectl apply -f pvc-clone.yaml
```
Проверяем что PV и PVC создался и статус PVC - `Bound`

7. Создаем pod, который будет смотреть на оригинальный PVC
```shell
kubectl apply -f deployment-clone.yaml
```
Ждем пока pod запустится

8. Проверяем наличие файла в клоне (аналогично п. 5) 




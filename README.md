# multizone-k8s-deploy
multizone kubernetes deployment example

Решение тестоаого задания:
https://docs.google.com/document/d/1Zl7Fj3HgLqc8Ecs_-W209S9UrpIbkji1_rgU9oK3jVU/edit#  

Решите задачу ниже. 

Код должен открываться в браузере, без архивов, GitHub будет идеальным.

Задача

Не ожидаем production-ready решения. Сделайте, как кажется правильным, опишите процесс поиска и принятые решения.

Опишите решение для веб-приложения в kubernetes в виде yaml-манифеста. Оставляйте в коде комментарии по принятым решениям. Есть следующие вводные:

у нас мультизональный кластер (три зоны), в котором пять нод
приложение требует около 5-10 секунд для инициализации
по результатам нагрузочного теста известно, что 4 пода справляются с пиковой нагрузкой
на первые запросы приложению требуется значительно больше ресурсов CPU, в дальнейшем потребление ровное в районе 0.1 CPU. По памяти всегда “ровно” в районе 128M memory
приложение имеет дневной цикл по нагрузке – ночью запросов на порядки меньше, пик – днём
хотим максимально отказоустойчивый deployment
хотим минимального потребления ресурсов от этого deployment’а

***


Для решения поставленной задачи создал деплоймент с HPA, affinity (чтобы распределить поды по зонам).
	Стратегия обновления RollingUpdate c максимальным количеством недоступных нод - одна.
	Создал CronJob для scale.
	Для CronJob создал ServiceAccount, RoleBinding, Role.

Замечания:
	Касательно CronJob, более актуальный способ сейчас - KEDA
		https://keda.sh/docs/2.10/scalers/cron/

Список используемой литературы:

kubernetes affinity multizone:
https://stackoverflow.com/questions/52457455/multizone-kubernetes-cluster-and-affinity-how-to-distribute-application-per-zon
https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/
	requiredDuringSchedulingIgnoredDuringExecution: Планировщик не может запланировать Pod, если не соблюдается правило. Эта функция похожа на nodeSelector, но с более выразительным синтаксисом.
	preferredDuringSchedulingIgnoredDuringExecution: планировщик пытается найти узел, соответствующий правилу. Если соответствующий узел недоступен, планировщик по-прежнему планирует Pod.

deployment example
https://ealebed.github.io/posts/2018/%D0%B7%D0%BD%D0%B0%D0%BA%D0%BE%D0%BC%D1%81%D1%82%D0%B2%D0%BE-%D1%81-kubernetes-%D1%87%D0%B0%D1%81%D1%82%D1%8C-5-deployments/

kubectl-scale scale comand
https://www.containiq.com/post/kubectl-scale

role and rolebindings
https://stackoverflow.com/questions/56205304/scale-kubernetes-deployments-via-api




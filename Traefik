**Traefik configuration for k8s With AWS challenge **

    kind: Service
    apiVersion: v1
    metadata:
      name: traefik-ingress-service
      namespace: kube-system
    spec:
      selector:
        k8s-app: traefik-ingress-lb
      ports:
        - protocol: TCP
          port: 80
          name: http
        - protocol: TCP
          port: 8080
          name: admin
      type: NodePort
    
    ---
    kind: Service
    apiVersion: v1
    metadata:
      name: traefik-ingress-service
      namespace: default
    spec:
      selector:
        k8s-app: traefik-ingress-lb
      ports:
        - protocol: TCP
          port: 80
          name: http
        - protocol: TCP
          port: 8080
          name: admin
      type: NodePort
    
    ---
    apiVersion: extensions/v1beta1
    kind: Ingress
    metadata:
      name: traefik
    spec:
      rules:
        - host: "HOST"
          http:
            paths:
              - path: /
                backend:
                  serviceName: front
                  servicePort: 80
    ---
    apiVersion: v1
    kind: ServiceAccount
    metadata:
      name: traefik-ingress-controller
      namespace: kube-system
    ---
    kind: DaemonSet
    apiVersion: apps/v1
    metadata:
      name: traefik-ingress-controller
      namespace: kube-system
      labels:
        k8s-app: traefik-ingress-lb
    spec:
      selector:
        matchLabels:
          k8s-app: traefik-ingress-lb
          name: traefik-ingress-lb
      template:
        metadata:
          labels:
            k8s-app: traefik-ingress-lb
            name: traefik-ingress-lb
        spec:
          serviceAccountName: traefik-ingress-controller
          terminationGracePeriodSeconds: 60
          volumes:
            - name: config-volume
              configMap:
                name: traefik-conf
          containers:
            - image: traefik:v1.7-alpine
              name: traefik-ingress-lb
              env:
                - name: AWS_ACCESS_KEY_ID
                  value: "ACCESS_KEY"
                - name: AWS_SECRET_ACCESS_KEY
                  value: "SECRET"
              volumeMounts:
                - name: config-volume
                  mountPath: /acme/traefik.toml
                  subPath: traefik.toml
              ports:
                - name: http
                  containerPort: 80
                  hostPort: 80
                - name: https
                  containerPort: 443
                  hostPort: 443
                - name: admin
                  containerPort: 8080
                  hostPort: 8080
              args:
                - --configfile=/acme/traefik.toml
                - --api
                - --kubernetes
                - --logLevel=DEBUG
    ---
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: traefik-conf
      namespace: kube-system
    data:
      traefik.toml: |
        defaultEntryPoints = ["http","https"]
        [entryPoints]
          [entryPoints.http]
          address = ":80"
          [entryPoints.http.redirect]
          entryPoint = "https"
          [entryPoints.https]
          address = ":443"
          [entryPoints.https.tls]
        [acme]
          email = "EMAIL"
          storage = "/acme/acme.json"
          entryPoint = "https"
          onDemand = true
          onHostRule = true
        [acme.dnsChallenge]
          provider = "route53"
          delayBeforeCheck = 0
        [[acme.domains]]
          main = "*.DOMAIN"

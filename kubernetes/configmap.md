# Kubernetes ConfigMap Notes
[Reference](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)

## Creating ConfigMaps
- Pass in a whole directory
    - `kubectl create configmap <name> --from-file=<directory>`
    - Will create ConfigMap w/ a dictionary where key is filename and value is contents of the file
    - What about subdirs?
    - What kind of files are valid? Any kind?

- Pass in multiple files
    - `kubectl create configmap <name> --from-file=<file1> --from-file=<file2> ...`

- Can override default key name when passing in a file
    - `kubectl create configmap <name> --from-file=<key_name>=<file>`

- Pass in "env file"
    - `kubectl create configmap <name> --from-env-file=<env_file>`
    - An env file is a file where every line is in the format `<key>=<value>`
    - Creates a ConfigMap where the contents are directly mapped to key:value pairs in the data field
    - Only the last file is used when multiple --from-env-file params are passed in

- Pass in literal value
    - `kubectl create configmap <name> --from-literal=<key1>=<value1> --from-literal=<key2>=<value2> ...`
    - Can mix --from-literal with --from-file flags

## Referencing ConfigMaps
- Use ConfigMap value as env var in Pod
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata: ...
    spec:
      containers:
        - name: ...
          image: ...
          command: ...
          env:
            - name: <env_var_name>
              valueFrom:
                configMapKeyRef:
                  name: <configmap_name>
                  key: <configmap_key>
    ```
    - Can specify values from multiple ConfigMaps

- Inject all values in a ConfigMap as env vars in a Pod
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata: ...
    spec:
      containers:
        - name: ...
          image: ...
          command: ...
          envFrom:
          - configMapRef:
              name: <configmap_name>
    ```
    - What about injecting multiple ConfigMaps?

- Use ConfigMap-defined env vars in Pod commands
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata: ...
    spec:
      containers:
        - name: ...
          image: ...
          command: [ "/bin/sh", "-c", "echo $(ENV_VAR_1) $(ENV_VAR_2)" ]
          env:
            - name: ENV_VAR_1
              valueFrom: ...
            - name: ENV_VAR_2
              valueFrom: ...
    ```

- Add ConfigMap data to a volume in a Pod
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata: ...
    spec:
      containers:
        - name: ...
          image: ...
          command: ...
          volumeMounts:
          - name: <volume_name>
            mountPath: <volume_path>
      volumes:
        - name: <volume_name>
          configMap:
            name: <configmap_name>
    ```
    - Mounts all files in the ConfigMap to the specified path

- Add ConfigMap data to a specific path in a volume
    ```yaml
    apiVersion: v1
    kind: Pod
    metadata: ...
    spec:
      containers: ...
      volumes:
        - name: <volume_name>
          configMap:
            name: <configmap_name>
            items:
            - key: <configmap_key>
              path: <path_relative_to_volume_path>
    ```

- Pods won't start if a ConfigMap that it references doesn't exist

Ah! That error usually happens because of **YAML indentation issues** or **improper use of multi-line strings**. In your earlier `downward-demo` YAML, the problem is most likely with the `args:` block:

```yaml
args:
  - while true; do
      echo "Env Vars:";
      echo "Pod: $MY_POD_NAME, Namespace: $MY_POD_NAMESPACE, Node: $MY_NODE_NAME";
      echo "CPU Request: $MY_CPU_REQUEST, Mem Limit: $MY_MEM_LIMIT";
      echo "Labels:"; cat /etc/podinfo/labels;
      echo "Annotations:"; cat /etc/podinfo/annotations;
      sleep 20;
    done;
```

**Problem:**

* YAML interprets `:` as a key/value separator.
* Using `;` or multi-line commands directly can confuse the parser.

---

### **Fix 1: Use `|-` for multi-line string**

```yaml
args:
  - |-
    while true; do
      echo "Env Vars:";
      echo "Pod: $MY_POD_NAME, Namespace: $MY_POD_NAMESPACE, Node: $MY_NODE_NAME";
      echo "CPU Request: $MY_CPU_REQUEST, Mem Limit: $MY_MEM_LIMIT";
      echo "Labels:"; cat /etc/podinfo/labels;
      echo "Annotations:"; cat /etc/podinfo/annotations;
      sleep 20;
    done
```

* `|-` tells YAML: treat everything below as **literal multi-line string**.
* No more parsing errors due to `:` or `;`.

---

### **Fix 2: Put the entire command in a single line**

```yaml
args:
  - sh -c 'while true; do echo "Env Vars:"; echo "Pod: $MY_POD_NAME, Namespace: $MY_POD_NAMESPACE, Node: $MY_NODE_NAME"; echo "CPU Request: $MY_CPU_REQUEST, Mem Limit: $MY_MEM_LIMIT"; echo "Labels:"; cat /etc/podinfo/labels; echo "Annotations:"; cat /etc/podinfo/annotations; sleep 20; done'
```

* Works, but less readable.


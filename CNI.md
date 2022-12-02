# CNI

Created time: November 19, 2022 12:09 AM
Last edited time: November 27, 2022 1:27 PM
Status: Todo
Type: Literature Note

# Container Networking Interface

OCI nedefinuje, jak ma probihat sitovani v kontejnerech. Toto si kazdy implementoval sam. Vestinou se jendalo o nasledujici kroky:

1. Create Namespace
2. Create bridge
3. Create VETH pair
4. Attach vEth to Namespace
5. Attach vEth to bridge
6. Assign IP addreses
7. Bring the interfaces up
8. Enable NAT
    1. Tim je mysleno pridat bridge IP, povolit forward packets a udelat NAT na bridge

Jelikoz se tento pattern opakoval, proto stacilo tento proces pouze zaobalit do jendoducheho programku `./proraam [add|delete] <container> <namespace>` Aby tento interfaces byl jasne definivany, byl vytvoren standart ******CNI******

## cni

**Je mnozina standardu, definijici interface pro sitovani kontejneru.**

**Programy, ktere implementuji tyto standary jsou oznacovany jako pluginy**

<aside>
⚠️ **CNI Plugin** **NENI** vyfikundace pridavajici nove veci do CNI
**CNI Plugin JE** implementace CNI standardu

</aside>

**Co CNI definuje sadu zodpovednosti**

- Container RUNtime
    - Vytvari nove interfaces
    - Urcuje site, ke kterym ma byt kontejner pripojen
    - Vola CNI plugin, kdyz je kontejner vytvoren
    - Vola CNI plugin, kdyz je kontejner smazan
    - Vytvari konfigurak pro vydefinovani nastaveni site
- CNI plugin
    - Musi podporovat ADD/DEL/CHECK command
    - Musi podporovat parametry cid(container ID) , network name space…
    - Prideluje IP adresy
    - Musi vracet jasne definovanou odpoved

## **Predefinovane CNI plugins**

- `bridge`
- `vlan`
- `ipvlan`
- `macvlan`
- `windows`
- `dhcp` - ipam
- `host-local` - ipam

<aside>
⚠️ DOCKER nepodporuje CNI, ale vlastni CNM

</aside>

---

# CNI definuje:

> 
> 
> 1. A format for administrators to define network configuration.
> 2. A protocol for container runtimes to make requests to network plugins.
> 3. A procedure for executing plugins based on a supplied configuration.
> 4. A procedure for plugins to delegate functionality to other plugins.
> 5. Data types for plugins to return their results to the runtime.

### ****Section 1: Network configuration format****

Jenda se o JSON, ktery si `CNI plugin` a contianer `runtime` predavaji.

```yaml
# CNI 
{
	cniVersion: "1.0.0", # verze CNI 
	name: "", #Network name
	disableCheck: True, #[True|False] if true runtime cannot call CHECK (kvuli ocekavanym erorum)
	Plugins: [...<plugin_object>] # List of CNI plugins a jejich konfigurace
}
#PLUGIN OBJECT
{
  type: "bridge", # nazev CNI pluginu na disku
	# optionals
	capabilities: {}, # Optional keys, used by the protocol:
	runtimeConfig: ...,
  agrs: ...,
  "cni.dev/*" : ...
  # optionals but defined
  ipMasq: True, # bool If supported by the plugin, sets up an IP masquerade on the host for this network. This is necessary if the host will act as a gateway to subnets that are not able to route to the IP assigned to the container.
  ipam: {}, # IPAM (IP Address Management)
	dns: {
		nameservers: [],
    domain: "",
    search: [],
    options: []
		},
}
```

### ****Section 2: Execution Protocol****

CNI definuje pouziti jako spousteni binarek (pluginu) container runtimem (`CR`). 

CNI plugins (programy implementujici CNI) se deli na 2 katregorie

- `interface plug` - vytvareji interafce v kontejner namespace a zajistuji spojeni s okolnim svetem
- `chained plugins` - upravuji jiz vytvoreno konfiguraci interfacu

Pri volani scriptu `CR` predava parametry pluginu pomoci environment variables a konfigurace.JKonfigurace je predavana pomoci `stdin`. Plugin vraci resoult (json) pomoci `stdout`

`CR` vola plugiin v runtime's networking domai. Vesinou se jedna o root namespace

- Parametry
    - `CNI_COMMAND`: indicates the desired operation; `ADD`, `DEL`, `CHECK`, or `VERSION`.
    - `CNI_CONTAINERID`: Container ID. A unique plaintext identifier for a container, allocated by the runtime. Must not be empty. Must start with an alphanumeric character, optionally followed by any combination of one or more alphanumeric characters, underscore (), dot (.) or hyphen (-).
    - `CNI_NETNS`: A reference to the container's "isolation domain". If using network namespaces, then a path to the network namespace (e.g. `/run/netns/[nsname]`)
    - `CNI_IFNAME`: Name of the interface to create inside the container; if the plugin is unable to use this interface name it must return an error.
    - `CNI_ARGS`: Extra arguments passed in by the user at invocation time. Alphanumeric key-value pairs separated by semicolons; for example, "FOO=BAR;ABC=123"
    - `CNI_PATH`: List of paths to search for CNI plugin executables. Paths are separated by an OS-specific list separator; for example ':' on Linux and ';' on Windows
- OperaceCNI
    - **ADD(** `CNI_CONTAINERID`, `CNI_NETNS`, `CNI_IFNAME`,*,`CNI_ARGS`, `CNI_PATH`**)**
        - Pridava container do site, neb modifikuje nastaveni
        - Pridava/upravuje inv `CNI_IFNAME`do kontejneru `CNI_NETNS`
    - **DEL(** `CNI_CONTAINERID`, `CNI_NETNS`, `CNI_IFNAME`,*,`CNI_ARGS`,`CNI_PATH`,`CNI_NETNS`**)**
        - Odebira container ze site, neb modifikuje nastaveni udelana `ADD` commandem
        - Maze/upravuje inv `CNI_IFNAME`z kontejneru `CNI_NETNS`
    - **CHECK**
        - Kontroluje nastaveni
    - **VERSION**

### ****Section 3: Execution of Network Configurations****

- ****Lifecycle & Ordering****
    - `CR`vytvori novy namespace
    - `CR` nesmi spustit paralelni CNI pro jeden kontejner
    - Plugins musi byt ready na to, aby byli spusteny na vice kontejneru
    - `CR` je zodpoveden za volani `delete`
    - `CR` uklizi nemaspaces
- ****Attachment Parameters****
    - 

### ****Section 4: Plugin Delegation****

- Nektere operace dava smysl delegvat na jinaci CNI plugin, Prikladme je sparva IP adres, aby nemuseli byt CNI pluginy tak velke, muhou svohji praci delegovat na mensi moduly (jeden pro IPv4 a druhy pro IPv6)
- Pro tento priklad vznikl i plagin `ipam` (IP Address Management Plugin)
- 

---

# Pouziti

- Vytvor interface
- Pridel IP
- Udelej diru do FW
- reportni, ze se to povedlo

---

- Related:
    1. 
- Otazky:
    1. Co je maskarada? (ipMasq)

- Source
    1. [https://www.youtube.com/watch?v=l2BS_kuQxBA&list=PL2We04F3Y_409TsxnyIZe8_bzTGPJ4EaH&index=4](https://www.youtube.com/watch?v=l2BS_kuQxBA&list=PL2We04F3Y_409TsxnyIZe8_bzTGPJ4EaH&index=4)
    2.
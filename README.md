# Habilitação de 3 GB de SWAP em Instância EC2 (Debian)

Este guia detalha o processo para criação, ativação e persistência de um arquivo de swap de 3 GB em uma instância EC2 baseada em Debian GNU/Linux.

---

## 📋 Requisitos

- Acesso SSH à instância EC2
- Permissões de `sudo`
- Espaço livre em disco suficiente (mínimo 3 GB)

---

## 🚀 Passo a passo

### 1. Acessar a instância via SSH

```bash
ssh -i ./maquina_teste.pem admin@<IP_DA_INSTANCIA>
```

---

### 2. Verificar uso de memória atual

```bash
free -h
```

**Exemplo de saída:**
```
Mem:   970Mi   used: 906Mi   free: 62Mi
Swap:    0B
```

---

### 3. Verificar containers ativos (opcional)

```bash
docker stats --no-stream
```

---

### 4. Criar o arquivo de swap (3 GB)

```bash
sudo dd if=/dev/zero of=/swapfile bs=1M count=3072 status=progress
```

---

### 5. Ajustar permissões

```bash
sudo chmod 600 /swapfile
```

---

### 6. Criar o espaço de swap

```bash
sudo mkswap /swapfile
```

---

### 7. Ativar o swap

```bash
sudo swapon /swapfile
```

---

### 8. Verificar se swap está ativo

```bash
free -h
```

**Saída esperada:**
```
Swap: 3.0Gi   used: 512Ki
```

---

### 9. Tornar o swap persistente (pós reboot)

```bash
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

---

### 10. (Opcional) Ajustar uso do swap

Reduzir o uso agressivo do swap definindo o `swappiness`:

```bash
sudo sysctl vm.swappiness=10
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

---

## 🧹 Caso precise resetar o swap

```bash
sudo swapoff /swapfile
sudo rm -f /swapfile
```

---

## ✅ Verificar swap ativo

```bash
swapon --show
```

Se o comando não existir, instale:

```bash
sudo apt update && sudo apt install util-linux -y
```

---

## 📌 Observações

- Este procedimento usa o disco da instância para armazenar o swap. Certifique-se de que o volume tem espaço suficiente.
- Para ambientes com tráfego intenso, considere criar swap em EBS separado com IOPS provisionadas, se necessário.

---
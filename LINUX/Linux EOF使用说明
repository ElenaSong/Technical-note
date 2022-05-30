# Linux EOF使用说明
## EOF基础用法
### Linux EOF介绍
EOF是END Of File的缩写,表示自定义终止符.既然自定义,那么EOF就不是固定的,可以随意设置别名,在linux按ctrl-d就代表EOF。
EOF通常配合cat进行多行文本输出。

使用如下：
  1. 文档追加
```
cat >>/test.txt <<EOF
echo "lin1......"
EOF
```
  2. 文档覆盖
```
cat >/test1.txt <<EOF
echo "line2......"
EOF
```
  3. 防止变量替换

EOF多行写入时会对内容中的 反引号`、$等，若不希望进行解析处理，有两种处理方式：
- \ 转译

   *EOF中需要转译内容较少时，可以对单个变量进行转译*
   ```
   cat >/etc/profile <<EOF
   PATH=\$PATH:\$HOME/bin
   EOF
   ```
- "EOF"、'EOF'、\EOF

   *适用于变量较多的情况*
    ```
    cat >/etc/profile <<'EOF'
    export PATH=$PATH:$HOME/bin
    export KUBECONFIG=~/.kube/config
    export GO_ROOT=$HOME/GO/bin
    EOF
    ```
---
## 嵌套EOF
某些情况下需要在文本1中增加增加文档，这就需要用到EOF的嵌套。需要说明的是，对于Linux来说，EOF是无意义的词语，所以，
仅仅需要将EOF替换成其他词语即可，如下：
```
cat > test.sh << "REALEND"
echo "获取环境信息"
MASTER_IP=$1
baseDomain=$(oc get cluster -o=jsonpath='{.spec.baseDomain}')
cluster_dns=${MASTER_IP}" api."${baseDomain}
cat >/home/hosts.txt << EOF
xx.xx.xx.xx baidu.com
${cluster_dns}
EOF
REALEND
```
## sshpass eeooff
背景：
   
   需要在外部访问集群，但是集群的dns非固定，所以需要登录openshift后台，获取环境的dns数据，并拷贝到当前目录。调试过程中
   由于EOF变量替换、叠加EOF等问题，花费了非常多的时间，心态也非常爆炸，统一记录如下。

**TIPS：sshpass eeooff是变化后的EOF。**

```
MASTER_IP=$1
sshpass -p "123456" ssh -o StrictHostKeyChecking=no root@${MASTER_IP} > /dev/null 2&1 << "eeooff"
cd /home && cat > test.sh << "REALEND"
echo "获取环境信息"
MASTER_IP=$1
baseDomain=$(oc get cluster -o=jsonpath='{.spec.baseDomain}')
cluster_dns=${MASTER_IP}" api."${baseDomain}
cat >/home/hosts.txt << EOF
xx.xx.xx.xx baidu.com
${cluster_dns}
EOF
REALEND
eeooff
sshpass -p "123456" ssh -o StrictHostKeyChecking=no root@${MASTER_IP} "sh /home/test.sh ${MASTER_IP}"
sshpass -p "123456" scp -o StrictHostKeyChecking=no root@${MASTER_IP}:/home/hosts.txt ./
sshpass -p "123456" scp -o StrictHostKeyChecking=no root@${MASTER_IP}:~/.kube/config ./
sshpass -p "123456" ssh -o StrictHostKeyChecking=no root@${MASTER_IP} > /dev/null 2&1 << eeooff
    rm -rf /home/hosts.txt
    rm -rf /home/test.sh
eeooff
```

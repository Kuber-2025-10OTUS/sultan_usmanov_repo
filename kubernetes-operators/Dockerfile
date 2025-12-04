FROM quay.io/operator-framework/ansible-operator:v1.27.0

COPY requirements.yml ${HOME}/requirements.yml

RUN pip install --upgrade ansible && /usr/bin/python3.8 -m pip install --upgrade pip
RUN ansible-galaxy collection install -r ${HOME}/requirements.yml \
 && chmod -R ug+rwx ${HOME}/.ansible
RUN ansible-galaxy collection install kubernetes.core --upgrade

COPY watches.yaml ${HOME}/watches.yaml
COPY roles/ ${HOME}/roles/
COPY playbooks/ ${HOME}/playbooks/
COPY ansible.cfg /etc/ansible/ansible.cfg
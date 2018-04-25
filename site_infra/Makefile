WEBSITE=bchain_site
CADDY_NAME=$(WEBSITE)-caddy
HOST_IP:=$(shell getent hosts `hostname`|cut -d\  -f1)


clean:
	$(MAKE) stop-caddy
	rm -r $(PWD)/.ssh
	rm /root/.ssh/authorized_keys

$(PWD)/.ssh:
	mkdir -p $(PWD)/.ssh/
	yes y |ssh-keygen -t rsa -N "" -f $(PWD)/.ssh/id_rsa

	ssh-keyscan -t ecdsa-sha2-nistp256 $(HOST_IP) >> $(PWD)/.ssh/known_hosts

/root/.ssh/authorized_keys: $(PWD)/.ssh/id_rsa.pub
	mkdir -p ~/.ssh
	cat $(PWD)/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

.PHONY: start-caddy
start-caddy: $(PWD)/.ssh /root/.ssh/authorized_keys
	docker run \
		--name "$(CADDY_NAME)" \
		--restart always \
		-v $(PWD)/Caddyfile:/etc/Caddyfile \
		-v $(PWD)/site:/site \
		-v $(PWD)/.caddy:/root/.caddy \
		-v $(PWD)/.ssh/:/root/.ssh \
		-e HOST_IP='$(HOST_IP)' \
		-e ACME_AGREE=true \
		-p 80:80 \
		-p 443:443 \
		-d \
		abiosoft/caddy

.PHONY: stop-caddy
stop-caddy:
	docker rm -f $(CADDY_NAME)
	rm -r $(PWD)/site

.PHONY: restart-caddy
restart-caddy:
	$(MAKE) stop-caddy
	$(MAKE) start-caddy

print-%:
	@echo '$*=$($*)'

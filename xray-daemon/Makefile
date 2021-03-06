include ../aws.env
include ecr.env

.PHONY: package tag login publish check-region check-repo check-account check-env

package:
	docker build -t xray-daemon .

check-repo:
	test -n "$(ECR_REPO)" || (echo "ECR_REPO must be defined in ecr.env"; exit 1)

check-account:
	test -n "$(ACCOUNT_ID)" || (echo "ACCOUNT_ID must be defined in ../aws.env"; exit 1)

check-region:
	test -n "$(AWS_REGION)" || (echo "AWS_REGION must be defined in ../aws.env"; exit 1)

check-env: check-repo check-region check-account

run-local: package check-env
	docker run --attach STDOUT -v ~/.aws/:/root/.aws/:ro --net=host -e AWS_REGION=$(AWS_REGION) --name xray-daemon xray-daemon -o

stop-local:
	docker stop xray-daemon && docker rm xray-daemon

tag: package check-env
	docker tag xray-daemon:latest $(ECR_REPO)

login: check-region
	@$(shell aws ecr get-login --no-include-email --region $(AWS_REGION))

publish: tag login check-env
	docker push $(ECR_REPO)

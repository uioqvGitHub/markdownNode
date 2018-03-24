application.yml Note 2018-03-20T09.03.55
========================
	server:
		port: 8080
		context-path: /projectName
	spring:
		datasource:
			username: scott
			password: tiger
			url: jdbc:oracle:thin:@localhost:1521:uioqv
			driverClassName: oracle.jdbc.OracleDriver
			type: org.apache.comments.dbcp.BasicDataSource
		mvc:
			view:
				prefix: /
				suffix:	.jsp
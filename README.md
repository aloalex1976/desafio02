# DESAFIO - Modelo de domínio e ORM

**Você deve criar um projeto no Spring Boot com Java e banco de dados H2, e implementar o modelo conceitual conforme especificação a seguir.**
**Além disso, você deve fazer o seeding da base de dados do modelo conceitual PARTICIPANTE, ATIVIDADE, CATEGORIA, BLOCO.**

**ESPECIFICAÇÃO - Sistema EVENTO:** 
Deseja-se construir um sistema para gerenciar as informações dos participantes das atividades de um evento acadêmico. As atividades deste evento podem ser, por exemplo, palestras, cursos, oficinas práticas, etc. Cada atividade que ocorre possui nome, descrição, preço, e pode ser dividida em vários blocos de horários (por exemplo: um curso de HTML pode ocorrer em dois blocos, sendo necessário armazenar o dia e os horários de início de fim do bloco daquele dia). Para cada participante, deseja-se cadastrar seu nome e email. 

## IMPORT.SQL

```java
INSERT INTO tb_categoria(descricao) VALUES ('Curso');
INSERT INTO tb_categoria(descricao) VALUES ('Oficina');

INSERT INTO tb_atividade (nome, descricao, preco, categoria_id) VALUES ('Curso de HTML', 'Aprenda HTML de forma pratica', 80.00, 1);
INSERT INTO tb_atividade (nome, descricao, preco, categoria_id) VALUES ('Oficina de Github', 'Controle versoes de seus projetos', 50.00, 2);

INSERT INTO tb_bloco (inicio, fim, atividade_id) VALUES (TIMESTAMP WITH TIME ZONE '2017-09-25T08:00:00Z', TIMESTAMP WITH TIME ZONE '2017-09-25T11:00:00Z', 1);
INSERT INTO tb_bloco (inicio, fim, atividade_id) VALUES (TIMESTAMP WITH TIME ZONE '2017-09-25T14:00:00Z', TIMESTAMP WITH TIME ZONE '2017-09-25T18:00:00Z', 2);
INSERT INTO tb_bloco (inicio, fim, atividade_id) VALUES (TIMESTAMP WITH TIME ZONE '2017-09-26T08:00:00Z', TIMESTAMP WITH TIME ZONE '2017-09-26T11:00:00Z', 2);

INSERT INTO tb_participante (nome, email) VALUES ('Jose Silva', 'jose@gmail.com');
INSERT INTO tb_participante (nome, email) VALUES ('Tiago Faria', 'tiago@gmail.com');
INSERT INTO tb_participante (nome, email) VALUES ('Maria do Rosario', 'maria@gmail.com');
INSERT INTO tb_participante (nome, email) VALUES ('Teresa Silva', 'teresa@gmail.com');

INSERT INTO tb_atividade_participante (atividade_id, participante_id) VALUES (1, 1);
INSERT INTO tb_atividade_participante (atividade_id, participante_id) VALUES (1, 3);
INSERT INTO tb_atividade_participante (atividade_id, participante_id) VALUES (2, 1);
INSERT INTO tb_atividade_participante (atividade_id, participante_id) VALUES (2, 3);
INSERT INTO tb_atividade_participante (atividade_id, participante_id) VALUES (2, 4);
```

## Trechos de código: Desafio02Applicatio.java

```java
package com.treinamento.desafio02;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Desafio02Application {

	public static void main(String[] args) {
		SpringApplication.run(Desafio02Application.class, args);
	}
}
```

### Categoria.java

```java
package com.treinamento.desafio02.entidades;

import java.util.ArrayList;
import java.util.List;
import java.util.Objects;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.OneToMany;
import jakarta.persistence.Table;

@Entity
@Table(name = "tb_categoria")
public class Categoria {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;
	
	@Column(columnDefinition = "TEXT")
	private String descricao;
	
	@OneToMany(mappedBy = "categoria")
	private List<Atividade> atividades = new ArrayList<>();
	
	public Categoria() {
		super();
		// TODO Auto-generated constructor stub
	}	
	
	public Categoria(Integer id, String descricao) {
		super();
		this.id = id;
		this.descricao = descricao;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getDescricao() {
		return descricao;
	}

	public void setDescricao(String descricao) {
		this.descricao = descricao;
	}
	
	public List<Atividade> getAtividades() {
		return atividades;
	}

	@Override
	public int hashCode() {
		return Objects.hash(descricao, id);
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Categoria other = (Categoria) obj;
		return Objects.equals(descricao, other.descricao) && Objects.equals(id, other.id);
	}
}
```





### Bloco.java

```java
package com.treinamento.desafio02.entidades;

import java.time.Instant;
import java.util.Objects;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;

@Entity
@Table(name = "tb_bloco")
public class Bloco {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;
	
	@Column(columnDefinition = "TIMESTAMP WITHOUT TIME ZONE")
	private Instant inicio;
	
	@Column(columnDefinition = "TIMESTAMP WITHOUT TIME ZONE")
	private Instant fim;
	
	@ManyToOne
	@JoinColumn(name = "atividade_id")
	private Atividade atividade;

	public Bloco() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Bloco(Integer id, Instant inicio, Instant fim, Atividade atividade) {
		super();
		this.id = id;
		this.inicio = inicio;
		this.fim = fim;
		this.atividade = atividade;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public Instant getInicio() {
		return inicio;
	}

	public void setInicio(Instant inicio) {
		this.inicio = inicio;
	}

	public Instant getFim() {
		return fim;
	}

	public void setFim(Instant fim) {
		this.fim = fim;
	}

	public Atividade getAtividade() {
		return atividade;
	}

	public void setAtividade(Atividade atividade) {
		this.atividade = atividade;
	}

	@Override
	public int hashCode() {
		return Objects.hash(id);
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Bloco other = (Bloco) obj;
		return Objects.equals(id, other.id);
	}
}
```




### Participante.java

```java
package com.treinamento.desafio02.entidades;

import java.util.HashSet;
import java.util.Objects;
import java.util.Set;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.ManyToMany;
import jakarta.persistence.Table;

@Entity
@Table(name = "tb_participante")
public class Participante {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;
	
	private String nome;
	
	@Column(unique = true)
	private String email;
	
	@ManyToMany(mappedBy = "participantes")
	private Set<Atividade> atividades = new HashSet<>();
	
	public Participante() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Participante(Integer id, String nome, String email, Set<Atividade> atividades) {
		super();
		this.id = id;
		this.nome = nome;
		this.email = email;
		this.atividades = atividades;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getNome() {
		return nome;
	}

	public void setNome(String nome) {
		this.nome = nome;
	}

	public String getEmail() {
		return email;
	}

	public void setEmail(String email) {
		this.email = email;
	}

	public Set<Atividade> getAtividades() {
		return atividades;
	}

	@Override
	public int hashCode() {
		return Objects.hash(id);
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Participante other = (Participante) obj;
		return Objects.equals(id, other.id);
	}
}
```





### Atividade.java

```java
package com.treinamento.desafio02.entidades;

import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Objects;
import java.util.Set;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.JoinTable;
import jakarta.persistence.ManyToMany;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.OneToMany;
import jakarta.persistence.Table;

@Entity
@Table(name = "tb_atividade")
public class Atividade {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer id;
	
	private String nome;
	
	@Column(columnDefinition = "TEXT")
	private String descricao;
	
	private Double preco;
	
	@ManyToOne
	@JoinColumn(name = "categoria_id")
	private Categoria categoria;
	
	@ManyToMany
	@JoinTable(name = "tb_atividade_participante",
	joinColumns = @JoinColumn(name = "atividade_id"),
	inverseJoinColumns = @JoinColumn(name = "participante_id"))
	private Set<Participante> participantes = new HashSet<>();
	
	@OneToMany(mappedBy = "atividade")
	private List<Bloco> blocos = new ArrayList<>();
	
	public Atividade() {
		super();
		// TODO Auto-generated constructor stub
	}

	public Atividade(Integer id, String nome, String descricao, Double preco, Categoria categoria) {
		super();
		this.id = id;
		this.nome = nome;
		this.descricao = descricao;
		this.preco = preco;
		this.categoria = categoria;
	}

	public Integer getId() {
		return id;
	}

	public void setId(Integer id) {
		this.id = id;
	}

	public String getNome() {
		return nome;
	}

	public void setNome(String nome) {
		this.nome = nome;
	}

	public String getDescricao() {
		return descricao;
	}

	public void setDescricao(String descricao) {
		this.descricao = descricao;
	}

	public Double getPrice() {
		return preco;
	}

	public void setPrice(Double price) {
		this.preco = price;
	}

	public Categoria getCategoria() {
		return categoria;
	}

	public void setCategoria(Categoria categoria) {
		this.categoria = categoria;
	}

	@Override
	public int hashCode() {
		return Objects.hash(id);
	}

	@Override
	public boolean equals(Object obj) {
		if (this == obj)
			return true;
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Atividade other = (Atividade) obj;
		return Objects.equals(id, other.id);
	}
}

```

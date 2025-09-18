<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <OutputType>WinExe</OutputType>
    <TargetFramework>net8.0-windows</TargetFramework>
    <Nullable>enable</Nullable>
    <UseWindowsForms>true</UseWindowsForms>
    <ImplicitUsings>enable</ImplicitUsings>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="MySql.Data" Version="9.4.0" />
  </ItemGroup>

</Project>
<?xml version="1.0" encoding="utf-8"?>
<Project ToolsVersion="Current" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <ItemGroup>
        <Compile Update="Form1.cs">
            <SubType>Form</SubType>
        </Compile>
    </ItemGroup>
</Project>


Microsoft Visual Studio Solution File, Format Version 12.00
# Visual Studio Version 17
VisualStudioVersion = 17.13.35931.197 d17.13
MinimumVisualStudioVersion = 10.0.40219.1
Project("{FAE04EC0-301F-11D3-BF4B-00C04F79EFBC}") = "CRUD", "CRUD.csproj", "{D14A815A-3C1A-4F94-8685-B6BA88A3DA05}"
EndProject
Global
	GlobalSection(SolutionConfigurationPlatforms) = preSolution
		Debug|Any CPU = Debug|Any CPU
		Release|Any CPU = Release|Any CPU
	EndGlobalSection
	GlobalSection(ProjectConfigurationPlatforms) = postSolution
		{D14A815A-3C1A-4F94-8685-B6BA88A3DA05}.Debug|Any CPU.ActiveCfg = Debug|Any CPU
		{D14A815A-3C1A-4F94-8685-B6BA88A3DA05}.Debug|Any CPU.Build.0 = Debug|Any CPU
		{D14A815A-3C1A-4F94-8685-B6BA88A3DA05}.Release|Any CPU.ActiveCfg = Release|Any CPU
		{D14A815A-3C1A-4F94-8685-B6BA88A3DA05}.Release|Any CPU.Build.0 = Release|Any CPU
	EndGlobalSection
	GlobalSection(SolutionProperties) = preSolution
		HideSolutionNode = FALSE
	EndGlobalSection
	GlobalSection(ExtensibilityGlobals) = postSolution
		SolutionGuid = {D5235B0D-EE95-413B-87D2-C971452D849E}
	EndGlobalSection
EndGlobal

using System;
using System.Windows.Forms;
using MySql.Data.MySqlClient;
using static System.Windows.Forms.VisualStyles.VisualStyleElement.ListView;
using static System.Windows.Forms.VisualStyles.VisualStyleElement;
namespace CRUD
{
    public partial class Form1 : Form
    {
        MySqlConnection Conexao;
        string connString = "datasource=localhost;username=root;" + "password=; database = agenda";
        public Form1()
        {
            InitializeComponent();
            //inicializando os componentes do meu listview, de acordo com a tabela no
            //SQL
            listView1.View = View.Details;
            listView1.Columns.Add("ID", 50);
            listView1.Columns.Add("Nome", 150);
            listView1.Columns.Add("Email", 150);
            listView1.Columns.Add("Telefone", 100);

        }
        private void btnSalvar_Click(object sender, EventArgs e)
        {
            try
            {

                //Criar a conex�o com o MySQL
                Conexao = new MySqlConnection(connString);
                //Cria a conex�o para inserir os dados
                string sql = "INSERT INTO contatos (nome,email,telefone) " +
                "VALUES ('" + txtNome.Text + "','" + txtEmail.Text + "'," +
                "'" + txtTelefone.Text + "')";
                //Cria o comando SQL
                MySqlCommand comando = new MySqlCommand(sql, Conexao);
                //Abre a conex�o
                Conexao.Open();
                comando.ExecuteNonQuery();
                MessageBox.Show("Contato salvo com sucesso!!!",
                "Sucesso", MessageBoxButtons.OK, MessageBoxIcon.Question);
            }
            catch (Exception ex)
            {
                MessageBox.Show("Erro ao salvar o contato!", "Erro", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
            finally
            {
                //fechando a conex�o
                Conexao.Close();
            }
        }
        private void button1_Click(object sender, EventArgs e)
        {
            try
            {
                //Cria a string SQL para consultar os dados
                string query = "'" + textBox1.Text + "'";
                //cria a conex�o com o mysql
                Conexao = new MySqlConnection(connString);
                //cria a string SQL para inserir dados
                string sql = "SELECT * " +
                "FROM contatos" +
                "WHERE nome LIKE " + query + "OR email like" + query;
                //CRIA O COMANDO SQL
                MySqlCommand comando = new MySqlCommand(sql, Conexao);
                //Abre a conex�o
                Conexao.Open();
                //Executa o comando
                MySqlDataReader reader = comando.ExecuteReader();
                //limpa a lista
                listView1.Items.Clear();
                //l� os dados retornados
                while (reader.Read())
                {
                    string[] row =
                    {
                    reader[0].ToString(),
                    reader[1].ToString(),
                    reader[2].ToString(),
                    reader[3].ToString(),
                    };
                    //Cria um novo ListView com os dados lidos
                    var linha_listview = new ListViewItem(row);
                    //adiciona o listViewItem � lista
                    listView1.Items.Add(linha_listview);
                }
            }
            catch (Exception ex)
            {
                MessageBox.Show("Erro ao consultar registro no banco" + ex.Message);
            }
            finally
            {
                //fecha a conex�o
                if (Conexao != null)
                {
                    Conexao.Close();
                }
            }
        }

        private void btnAtualizar_Click(object sender, EventArgs e)
        {
            try
            {
                Conexao = new MySqlConnection(connString);

                string sql = "UPDATE contatos SET nome = @nome, email = @email, telefone = @telefone WHERE id = @id";

                MySqlCommand comando = new MySqlCommand(sql, Conexao);

                comando.Parameters.AddWithValue("@nome", txtNome.Text);
                comando.Parameters.AddWithValue("@email", txtEmail.Text);
                comando.Parameters.AddWithValue("@telefone", txtTelefone.Text);
                comando.Parameters.AddWithValue("@id", txtID.Text);

                Conexao.Open();

                comando.ExecuteNonQuery();

                MessageBox.Show("Contato atualizado com sucesso!!!", "Sucesso", MessageBoxButtons.OK, MessageBoxIcon.Information);
            }
            catch (Exception ex)
            {
                MessageBox.Show("Erro ao atualizar o contato!!! \n\n" + ex.Message, "Erro", MessageBoxButtons.OK, MessageBoxIcon.Error);
            }
            finally
            {
                Conexao.Close();
            }
        }

        private void btnExcluir_Click(object sender, EventArgs e)
        {
            // Obt�m o nome do campo de texto
            string nomeParaExcluir = txtNome.Text;

            // Verifica se o campo Nome n�o est� vazio
            if (string.IsNullOrWhiteSpace(nomeParaExcluir))
            {
                MessageBox.Show("Por favor, digite o Nome para excluir.", "Aviso", MessageBoxButtons.OK, MessageBoxIcon.Warning);
                return;
            }

            // Pergunta ao usu�rio se ele tem certeza da exclus�o
            DialogResult confirmacao = MessageBox.Show(
                $"Tem certeza que deseja excluir o registro com o Nome '{nomeParaExcluir}'?",
                "Confirmar Exclus�o", MessageBoxButtons.YesNo, MessageBoxIcon.Question);

            if (confirmacao == DialogResult.Yes)
            {
                // Use a string de conex�o do seu banco de dados
                string connectionString = "Sua_string_de_conexao_aqui";

                // Cria a instru��o SQL para exclus�o, usando um par�metro para evitar SQL Injection
                string query = "DELETE FROM SuaTabela WHERE Nome = @Nome";

                using (MySqlConnection conexao = new MySqlConnection(connectionString))
                {
                    using (MySqlCommand comando = new MySqlCommand(query, conexao))
                    {
                        // Adiciona o par�metro
                        comando.Parameters.AddWithValue("@Nome", nomeParaExcluir);

                        try
                        {
                            conexao.Open();
                            int linhasAfetadas = comando.ExecuteNonQuery();

                            if (linhasAfetadas > 0)
                            {
                                MessageBox.Show($"{linhasAfetadas} registro(s) exclu�do(s) com sucesso!", "Sucesso", MessageBoxButtons.OK, MessageBoxIcon.Information);
                            }
                            else
                            {
                                MessageBox.Show("Nenhum registro encontrado com este Nome.", "Aviso", MessageBoxButtons.OK, MessageBoxIcon.Information);
                            }
                        }
                        catch (Exception ex)
                        {
                            MessageBox.Show($"Ocorreu um erro ao tentar excluir: {ex.Message}", "Erro", MessageBoxButtons.OK, MessageBoxIcon.Error);
                        }
                    }
                }
            }
        }
    }
}
namespace CRUD
{
    partial class Form1
    {
        /// <summary>
        ///  Required designer variable.
        /// </summary>
        private System.ComponentModel.IContainer components = null;

        /// <summary>
        ///  Clean up any resources being used.
        /// </summary>
        /// <param name="disposing">true if managed resources should be disposed; otherwise, false.</param>
        protected override void Dispose(bool disposing)
        {
            if (disposing && (components != null))
            {
                components.Dispose();
            }
            base.Dispose(disposing);
        }

        #region Windows Form Designer generated code

        /// <summary>
        ///  Required method for Designer support - do not modify
        ///  the contents of this method with the code editor.
        /// </summary>
        private void InitializeComponent()
        {
            txtNome = new Label();
            txtEmail = new Label();
            txtTelefone = new Label();
            txtID = new Label();
            txtLocalizar = new Label();
            btnSalvar = new Button();
            btnAtualizar = new Button();
            btnConsultar = new Button();
            textBox1 = new TextBox();
            textBox2 = new TextBox();
            textBox3 = new TextBox();
            textBox4 = new TextBox();
            textBox5 = new TextBox();
            listView1 = new ListView();
            btnExcluir = new Button();
            SuspendLayout();
            // 
            // txtNome
            // 
            txtNome.AutoSize = true;
            txtNome.Location = new Point(127, 61);
            txtNome.Name = "txtNome";
            txtNome.Size = new Size(43, 15);
            txtNome.TabIndex = 0;
            txtNome.Text = "Nome:";
            // 
            // txtEmail
            // 
            txtEmail.AutoSize = true;
            txtEmail.Location = new Point(127, 102);
            txtEmail.Name = "txtEmail";
            txtEmail.Size = new Size(44, 15);
            txtEmail.TabIndex = 1;
            txtEmail.Text = "E-mail:";
            // 
            // txtTelefone
            // 
            txtTelefone.AutoSize = true;
            txtTelefone.Location = new Point(127, 146);
            txtTelefone.Name = "txtTelefone";
            txtTelefone.Size = new Size(54, 15);
            txtTelefone.TabIndex = 2;
            txtTelefone.Text = "Telefone:";
            // 
            // txtID
            // 
            txtID.AutoSize = true;
            txtID.Location = new Point(127, 190);
            txtID.Name = "txtID";
            txtID.Size = new Size(21, 15);
            txtID.TabIndex = 3;
            txtID.Text = "ID:";
            // 
            // txtLocalizar
            // 
            txtLocalizar.AutoSize = true;
            txtLocalizar.Location = new Point(395, 59);
            txtLocalizar.Name = "txtLocalizar";
            txtLocalizar.Size = new Size(56, 15);
            txtLocalizar.TabIndex = 4;
            txtLocalizar.Text = "Localizar:";
            // 
            // btnSalvar
            // 
            btnSalvar.Location = new Point(127, 245);
            btnSalvar.Name = "btnSalvar";
            btnSalvar.Size = new Size(75, 23);
            btnSalvar.TabIndex = 5;
            btnSalvar.Text = "Salvar";
            btnSalvar.UseVisualStyleBackColor = true;
            btnSalvar.Click += btnSalvar_Click;
            // 
            // btnAtualizar
            // 
            btnAtualizar.Location = new Point(227, 245);
            btnAtualizar.Name = "btnAtualizar";
            btnAtualizar.Size = new Size(75, 23);
            btnAtualizar.TabIndex = 6;
            btnAtualizar.Text = "Atualizar";
            btnAtualizar.UseVisualStyleBackColor = true;
            btnAtualizar.Click += btnAtualizar_Click;
            // 
            // btnConsultar
            // 
            btnConsultar.Location = new Point(677, 77);
            btnConsultar.Name = "btnConsultar";
            btnConsultar.Size = new Size(75, 23);
            btnConsultar.TabIndex = 8;
            btnConsultar.Text = "Consultar";
            btnConsultar.UseVisualStyleBackColor = true;
            // 
            // textBox1
            // 
            textBox1.Location = new Point(127, 76);
            textBox1.Name = "textBox1";
            textBox1.Size = new Size(207, 23);
            textBox1.TabIndex = 9;
            // 
            // textBox2
            // 
            textBox2.Location = new Point(127, 120);
            textBox2.Name = "textBox2";
            textBox2.Size = new Size(207, 23);
            textBox2.TabIndex = 10;
            // 
            // textBox3
            // 
            textBox3.Location = new Point(127, 164);
            textBox3.Name = "textBox3";
            textBox3.Size = new Size(207, 23);
            textBox3.TabIndex = 11;
            // 
            // textBox4
            // 
            textBox4.Location = new Point(127, 208);
            textBox4.Name = "textBox4";
            textBox4.Size = new Size(207, 23);
            textBox4.TabIndex = 12;
            // 
            // textBox5
            // 
            textBox5.Location = new Point(395, 77);
            textBox5.Name = "textBox5";
            textBox5.Size = new Size(276, 23);
            textBox5.TabIndex = 13;
            // 
            // listView1
            // 
            listView1.Location = new Point(395, 106);
            listView1.Name = "listView1";
            listView1.Size = new Size(357, 297);
            listView1.TabIndex = 14;
            listView1.UseCompatibleStateImageBehavior = false;
            // 
            // btnExcluir
            // 
            btnExcluir.Location = new Point(127, 291);
            btnExcluir.Name = "btnExcluir";
            btnExcluir.Size = new Size(76, 24);
            btnExcluir.TabIndex = 15;
            btnExcluir.Text = "Excluir";
            btnExcluir.UseVisualStyleBackColor = true;
            btnExcluir.Click += btnExcluir_Click;
            // 
            // Form1
            // 
            AutoScaleDimensions = new SizeF(7F, 15F);
            AutoScaleMode = AutoScaleMode.Font;
            ClientSize = new Size(800, 450);
            Controls.Add(btnExcluir);
            Controls.Add(listView1);
            Controls.Add(textBox5);
            Controls.Add(textBox4);
            Controls.Add(textBox3);
            Controls.Add(textBox2);
            Controls.Add(textBox1);
            Controls.Add(btnConsultar);
            Controls.Add(btnAtualizar);
            Controls.Add(btnSalvar);
            Controls.Add(txtLocalizar);
            Controls.Add(txtID);
            Controls.Add(txtTelefone);
            Controls.Add(txtEmail);
            Controls.Add(txtNome);
            Name = "Form1";
            Text = "Form1";
            ResumeLayout(false);
            PerformLayout();
        }

        #endregion

        private Label txtNome;
        private Label txtEmail;
        private Label txtTelefone;
        private Label txtID;
        private Label txtLocalizar;
        private Button btnSalvar;
        private Button btnAtualizar;
        private Button btnConsultar;
        private TextBox textBox1;
        private TextBox textBox2;
        private TextBox textBox3;
        private TextBox textBox4;
        private TextBox textBox5;
        private ListView listView1;
        private Button btnExcluir;
    }
}

<?xml version="1.0" encoding="utf-8"?>
<root>
  <!--
    Microsoft ResX Schema

    Version 2.0

    The primary goals of this format is to allow a simple XML format
    that is mostly human readable. The generation and parsing of the
    various data types are done through the TypeConverter classes
    associated with the data types.

    Example:

    ... ado.net/XML headers & schema ...
    <resheader name="resmimetype">text/microsoft-resx</resheader>
    <resheader name="version">2.0</resheader>
    <resheader name="reader">System.Resources.ResXResourceReader, System.Windows.Forms, ...</resheader>
    <resheader name="writer">System.Resources.ResXResourceWriter, System.Windows.Forms, ...</resheader>
    <data name="Name1"><value>this is my long string</value><comment>this is a comment</comment></data>
    <data name="Color1" type="System.Drawing.Color, System.Drawing">Blue</data>
    <data name="Bitmap1" mimetype="application/x-microsoft.net.object.binary.base64">
        <value>[base64 mime encoded serialized .NET Framework object]</value>
    </data>
    <data name="Icon1" type="System.Drawing.Icon, System.Drawing" mimetype="application/x-microsoft.net.object.bytearray.base64">
        <value>[base64 mime encoded string representing a byte array form of the .NET Framework object]</value>
        <comment>This is a comment</comment>
    </data>

    There are any number of "resheader" rows that contain simple
    name/value pairs.

    Each data row contains a name, and value. The row also contains a
    type or mimetype. Type corresponds to a .NET class that support
    text/value conversion through the TypeConverter architecture.
    Classes that don't support this are serialized and stored with the
    mimetype set.

    The mimetype is used for serialized objects, and tells the
    ResXResourceReader how to depersist the object. This is currently not
    extensible. For a given mimetype the value must be set accordingly:

    Note - application/x-microsoft.net.object.binary.base64 is the format
    that the ResXResourceWriter will generate, however the reader can
    read any of the formats listed below.

    mimetype: application/x-microsoft.net.object.binary.base64
    value   : The object must be serialized with
            : System.Runtime.Serialization.Formatters.Binary.BinaryFormatter
            : and then encoded with base64 encoding.

    mimetype: application/x-microsoft.net.object.soap.base64
    value   : The object must be serialized with
            : System.Runtime.Serialization.Formatters.Soap.SoapFormatter
            : and then encoded with base64 encoding.

    mimetype: application/x-microsoft.net.object.bytearray.base64
    value   : The object must be serialized into a byte array
            : using a System.ComponentModel.TypeConverter
            : and then encoded with base64 encoding.
    -->
  <xsd:schema id="root" xmlns="" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:msdata="urn:schemas-microsoft-com:xml-msdata">
    <xsd:import namespace="http://www.w3.org/XML/1998/namespace" />
    <xsd:element name="root" msdata:IsDataSet="true">
      <xsd:complexType>
        <xsd:choice maxOccurs="unbounded">
          <xsd:element name="metadata">
            <xsd:complexType>
              <xsd:sequence>
                <xsd:element name="value" type="xsd:string" minOccurs="0" />
              </xsd:sequence>
              <xsd:attribute name="name" use="required" type="xsd:string" />
              <xsd:attribute name="type" type="xsd:string" />
              <xsd:attribute name="mimetype" type="xsd:string" />
              <xsd:attribute ref="xml:space" />
            </xsd:complexType>
          </xsd:element>
          <xsd:element name="assembly">
            <xsd:complexType>
              <xsd:attribute name="alias" type="xsd:string" />
              <xsd:attribute name="name" type="xsd:string" />
            </xsd:complexType>
          </xsd:element>
          <xsd:element name="data">
            <xsd:complexType>
              <xsd:sequence>
                <xsd:element name="value" type="xsd:string" minOccurs="0" msdata:Ordinal="1" />
                <xsd:element name="comment" type="xsd:string" minOccurs="0" msdata:Ordinal="2" />
              </xsd:sequence>
              <xsd:attribute name="name" type="xsd:string" use="required" msdata:Ordinal="1" />
              <xsd:attribute name="type" type="xsd:string" msdata:Ordinal="3" />
              <xsd:attribute name="mimetype" type="xsd:string" msdata:Ordinal="4" />
              <xsd:attribute ref="xml:space" />
            </xsd:complexType>
          </xsd:element>
          <xsd:element name="resheader">
            <xsd:complexType>
              <xsd:sequence>
                <xsd:element name="value" type="xsd:string" minOccurs="0" msdata:Ordinal="1" />
              </xsd:sequence>
              <xsd:attribute name="name" type="xsd:string" use="required" />
            </xsd:complexType>
          </xsd:element>
        </xsd:choice>
      </xsd:complexType>
    </xsd:element>
  </xsd:schema>
  <resheader name="resmimetype">
    <value>text/microsoft-resx</value>
  </resheader>
  <resheader name="version">
    <value>2.0</value>
  </resheader>
  <resheader name="reader">
    <value>System.Resources.ResXResourceReader, System.Windows.Forms, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089</value>
  </resheader>
  <resheader name="writer">
    <value>System.Resources.ResXResourceWriter, System.Windows.Forms, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089</value>
  </resheader>
</root>
namespace CRUD
{
    internal static class Program
    {
        /// <summary>
        ///  The main entry point for the application.
        /// </summary>
        [STAThread]
        static void Main()
        {
            // To customize application configuration such as set high DPI settings or default font,
            // see https://aka.ms/applicationconfiguration.
            ApplicationConfiguration.Initialize();
            Application.Run(new Form1());
        }
    }
}

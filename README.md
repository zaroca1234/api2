using OrnamentationProject.Models;

namespace OrnamentationProject.controles
{
    public class ClienteController
    {
       

        // Otras funciones útiles según sea necesario


        // Los otros controladores (PuertaController, OrdenController, EstiloController, ProveedorController, VentanaController) seguirían un patrón similar.
    } 
}


using DocumentFormat.OpenXml.Office2010.Excel;
using Microsoft.AspNetCore.Mvc;
using Microsoft.EntityFrameworkCore.Metadata.Internal;
using OrnamentationProject.Models;
using OrnamentationProject.servicios;
using System;

namespace OrnamentationProject.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        private static readonly string[] Summaries = new[]
        {
            "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
        };

        private readonly ILogger<WeatherForecastController> _logger;

        public WeatherForecastController(ILogger<WeatherForecastController> logger)
        {
            _logger = logger;
        }

        [HttpGet(Name = "GetWeatherForecast")]
        public IEnumerable<WeatherForecast> Get()
        {
            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = DateOnly.FromDateTime(DateTime.Now.AddDays(index)),
                TemperatureC = Random.Shared.Next(-20, 55),
                Summary = Summaries[Random.Shared.Next(Summaries.Length)]
            })
            .ToArray();
        }


        
        private ClienteService clienteService;

        public object HttpStatus { get; private set; }


       
        public ResponseEntity<Clients> obtenerClientePorId(@PathVariable  Id)
            {
                Clients cliente = clienteService.obtenerClientePorId(Id);
                return ResponseEntity.ok(cliente);
            }
         
            

    }

    internal class ResponseEntity
    {
        internal static ResponseEntity<Clients> ok(Clients cliente)
        {
            throw new NotImplementedException();
        }

        internal static object status(object cREATED)
        {
            throw new NotImplementedException();
        }

    }
}



using Microsoft.EntityFrameworkCore;
using OrnamentationProject.Models;
using WebApplication2.Model;

namespace OrnamentationProject.DbContexts
{
    public class OrnamentationProjectDbContexts: DbContext
    {

        public DbSet<Clients> Users { get; set; }
        public DbSet<EstadisticasJugador> estadisticasJugador { get; set; }
        public DbSet<Orders> orders { get; set; }
        public DbSet<MejoraGranja> MEjoraPaneles { get; set; }
        public DbSet<Mision> mision { get; set; }
        public DbSet<Recurso> recursos { get; set; }
        public DbSet<TransaccionMercado> mercados { get; set; }
        public DbSet<UserType> usertypes { get; set; }

        public OrnamentationProjectDbContexts(DbContextOptions<OrnamentationProjectDbContexts>options) : base(options) { }

    }
}
using Microsoft.EntityFrameworkCore;
using System.ComponentModel.DataAnnotations;

namespace OrnamentationProject.Models
{
    public class Clients
    {
        [Key] public int idCliente { get; set; }

        public string nombre { get; set; }
        public string apellido { get; set; }

        public string fechaNacimiento { get; set; }
        public string telefono { get; set; }

       

        
    }
}
namespace WebApplication2.Model
{
    public class EstadisticasJugador
    {
        public int Id { get; set; }
        public int JugadorId { get; set; }
        public string Jugador { get; set; }
        public int Experiencia { get; set; }
        public int Nivel { get; set; }
        public int Monedas { get; set; }
        
    }
}
namespace WebApplication2.Model
{
    public class Mision
    {
        public int Id { get; set; }
        public string Nombre { get; set; }
        public string Descripcion { get; set; }
        public int NivelRequerido { get; set; }
        public int RecompensaExperiencia { get; set; }
        public int RecompensaMonedas { get; set; }
        
    }
}
using System.ComponentModel.DataAnnotations;

namespace OrnamentationProject.Models
{
    public class Orders
    {
        [Key] public int idPedido { get; set; }
        public int iddCliente { get; set; }
        public int iddPuerta { get; set; }
        public int iddVentana { get; set; } 


    }
}
namespace WebApplication2.Model
{
    public class Recurso
    {
        public int Id { get; set; }
        public string Nombre { get; set; }
       
    }
}
namespace WebApplication2.Model
{
    public class TransaccionMercado
    {
        public int Id { get; set; }
        public int JugadorId { get; set; }
        public  string Jugador { get; set; }
        public int CultivoId { get; set; }
        public string Cultivo { get; set; }
        public int Cantidad { get; set; }
        public decimal PrecioUnitario { get; set; }
        public DateTime Fecha { get; set; }
       
    }

}
namespace WebApplication2.Model
{
    public class UserType
    {
        public int UserTypeId { get; set; }
        public string? UserTypeName { get; set; }
    }
}

using DotLiquid.Tags;
using NodaTime;
using NuGet.Protocol.Core.Types;
using OrnamentationProject.Models;
using Microsoft.EntityFrameworkCore;
using System.Threading.Tasks;
using DocumentFormat.OpenXml.Spreadsheet;
using OrnamentationProject.DbContexts;

namespace WebApplication2.Repositories
{

    public interface IUserRepository
    {
        Task<List<Clients>> GetAll();
        Task<Clients> GetUser(int idUser);
        Task<Clients> CreateUser(string name, string lastName, string email, string phone, int userTypeId);
        Task<Clients> UpdateUser(Clients user);
        Task<Clients> DeleteUser(int idUser);
    }


    public class UserRepository : IUserRepository
    {

        private readonly OrnamentationProjectDbContexts _db;
        private object fechaNacimiento;

        public UserRepository(OrnamentationProjectDbContexts db)
        {
            _db = db;
        }

        public async Task<List<Clients>> GetAll()
        {
            return await _db.Users.ToListAsync();
        }


        public async Task<Clients> GetUser(int idUser)
        {
            return await _db.Users.FirstOrDefaultAsync(u => u.idCliente == idUser);
        }


        public async Task<Clients> CreateUser(string name, string lastName, string email, string phone, int userTypeId)
        {
            Clients newUser = new Clients
            {
                nombre = name,
                apellido = lastName,
                fechaNacimiento = email,
                telefono = phone,
                idCliente = userTypeId

            };

            await _db.Users.AddAsync(newUser);
            await _db.SaveChangesAsync();
            return newUser;
        }


        public async Task<Clients> UpdateUser(Clients user)
        {
            _db.Users.Update(user);
            await _db.SaveChangesAsync();
            return user;
        }
        public async Task<Clients> DeleteUser(int idUser)
        {
            Clients user = await GetUser(idUser);

            // user.IsDeleted = true;
            return await UpdateUser(user);



        }
    }
}

using DocumentFormat.OpenXml.Drawing.Charts;

namespace OrnamentationProject.repositories
{
    public class userRepository
    {
    }

    

namespace WebApplication2.Repositories
    {

        public interface IUserTypeRepository
        {
            List<Order> GetAll();
            Order Get(int id);
            void CreateUserType(Order userType);
            void UpdateUserType(Order userType);
            void DeleteUserType(int id);
        }

        public class UserTypeRepository : IUserTypeRepository
        {
            public void CreateUserType(Order userType)
            {
                throw new NotImplementedException();
            }

            public void DeleteUserType(int id)
            {
                throw new NotImplementedException();
            }

            public Order Get(int id)
            {
                throw new NotImplementedException();
            }

            public List<Order> GetAll()
            {
                throw new NotImplementedException();
            }

            public void UpdateUserType(Order userType)
            {
                throw new NotImplementedException();
            }

            Order IUserTypeRepository.Get(int id)
            {
                throw new NotImplementedException();
            }

            List<Order> IUserTypeRepository.GetAll()
            {
                throw new NotImplementedException();
            }
        }

    }
}

using OrnamentationProject.Models;

namespace OrnamentationProject.servicios
{
    internal class ClienteRepository
    {
        internal List<Clients> findAll()
        {
            throw new NotImplementedException();
        }

        internal Clients save(long id)
        {
            throw new NotImplementedException();
        }

        internal Clients save(Clients cliente)
        {
            throw new NotImplementedException();
        }
    }
}using DocumentFormat.OpenXml.Spreadsheet;
using DotLiquid.Util;
using OpenQA.Selenium;
using OrnamentationProject.Controllers;
using OrnamentationProject.Models;
using OrnamentationProject.repositories;
using WebApplication2.Repositories;
using static System.Runtime.InteropServices.JavaScript.JSType;

namespace OrnamentationProject.servicios
{
    
        public class ClienteService
        {

            
            private ClienteRepository clienteRepository;

            public List<Clients> obtenerTodosLosClientes()
            {
                return clienteRepository.findAll();
            }

        public Clients obtenerClientePorId(long id)
        {
            return clienteRepository.save(id);
        }

        public Clients guardarCliente(Clients cliente)
            {
           
                return clienteRepository.save(cliente);
            }

            public void eliminarCliente(int id)
            {
                Clients cliente = obtenerClientePorId(id);
                clienteRepository.Equals(cliente);
            }

        internal void eliminarCliente(PathVariable id)
        {
            throw new NotImplementedException();
        }

        internal Clients obtenerClientePorId(PathVariable id)
        {
            throw new NotImplementedException();
        }

        public interface IUserService
        {

            Task<List<Clients>> GetAll();
            Task<Clients> GetUser(int idUser);
            Task<Clients> CreateUser(string name, string lastName, string email, string phone, int userTypeId);
            Task<Clients> UpdateUser(int idUser, string? name = null, string? lastName = null, string? email = null, string? phone = null, int? userTypeId = null);
            Task<Clients> DeleteUser(int idUser);
        }

        public class UserService : IUserService
        {
            private readonly IUserRepository _userRepository;
            public UserService(IUserRepository userRepository)
            {
                _userRepository = userRepository;
            }

            public async Task<Clients> CreateUser(string name, string lastName, string email, string phone, int userTypeId)
            {
                return await _userRepository.CreateUser(name, lastName, email, phone, userTypeId);
            }

            public async Task<Clients> DeleteUser(int idUser)
            {
                return await _userRepository.DeleteUser(idUser);
            }

            public async Task<List<Clients>> GetAll()
            {
                return await _userRepository.GetAll();

            }

            public async Task<Clients> GetUser(int idUser)
            {
                return await _userRepository.GetUser(idUser);
            }

            public async Task<Clients> UpdateUser(int idUser, string? name = null, string? lastName = null, string? email = null, string? phone = null, int? userTypeId = null)
            {
                Clients user = await _userRepository.GetUser(idUser);
                if (user == null)
                {
                    return null;
                }
                else
                {
                    if (name != null)
                    {
                        user.nombre = name;
                    }
                    if (lastName != null)
                    {
                        user.apellido = lastName;
                    }
                    if (email != null)
                    {
                        user.fechaNacimiento = email;
                    }
                    if (phone != null)
                    {
                        user.telefono= phone;
                    }
                    if (userTypeId != null)
                    {
                        user.idCliente = (int)userTypeId;
                    }
                }
                return await _userRepository.UpdateUser(user);
            }

            Task<Clients> IUserService.CreateUser(string name, string lastName, string email, string phone, int userTypeId)
            {
                throw new NotImplementedException();
            }

            Task<Clients> IUserService.DeleteUser(int idUser)
            {
                throw new NotImplementedException();
            }

            Task<List<Clients>> IUserService.GetAll()
            {
                throw new NotImplementedException();
            }

            Task<Clients> IUserService.GetUser(int idUser)
            {
                throw new NotImplementedException();
            }

            Task<Clients> IUserService.UpdateUser(int idUser, string? name, string? lastName, string? email, string? phone, int? userTypeId)
            {
                throw new NotImplementedException();
            }
        }
    }



       
    
}
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning"
    }
  },
  "ConnectionStrings": {
    "MyConnection": "Data Source = DESKTOP-64QOPD3\\MSSQLSERVER01; Database = OrnamentationProjectDb; Integrated Security = True;TrustServerCertificate=True"
  },
  "AllowedHosts": "*"
}
using Microsoft.EntityFrameworkCore;
using OrnamentationProject.DbContexts;

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
var conectionString = builder.Configuration.GetConnectionString("MyConnection");
builder.Services.AddDbContext<OrnamentationProjectDbContexts>(options => options.UseSqlServer(conectionString));
builder.Services.AddControllers();
// Learn more about configuring Swagger/OpenAPI at https://aka.ms/aspnetcore/swashbuckle
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

// Configure the HTTP request pipeline.
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

app.UseAuthorization();

app.MapControllers();

app.Run();
